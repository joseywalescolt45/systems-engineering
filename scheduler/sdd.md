![Arrow Banner](https://github.com/Arrow-air/.github/raw/main/profile/assets/arrow_v2_twitter-banner_neu.png)

# Scheduler - Software Design Document (SDD)

## Overview

This document details the software implementation of the aircraft scheduler.

This process is responsible for creating and modifying flight plans.

There will be >= 1 `scheduler` instances running concurrently. Each instance will be limited to a "region". Regions are usually centered on a major city with some exceptions, such as popular remote tourist destinations.

This allows several benefits:
+ Smaller number of pads for computation
  + Won't include pads thousands of miles away in routing algorithm
+ Incremental rollout of new scheduler optimizations
  + Beta testing in specific regions
+ Down time in one region won't affect other regions

At some point in Arrow's growth, regional scheduler processes will need to coordinate with one another to facilitate cross-region transfers. This would be the case in adjacent cities whose Vertiport networks grow large enough to intersect. At this future point, Arrow will need a policy defining when aircraft are allowed to transfer regions. Potentially a load balancing feature.

Attribute | Description
--- | ---
Status | Draft

## Related Documents

Document | Description
--- | ---
[Concept of Operations (CONOPS)](https://docs.google.com/document/d/1AZyucyNBihml7bqxxxKISegdeacpRQwPmsbD6xJcLCQ/edit) | Overview of the scope and duties of this module.
[Requirements & User Stories](https://docs.google.com/spreadsheets/d/1hLGIY6v_-GWK0jljLleN9nCiaRth0uKqRwssCJGIpuo/edit#gid=0) | Requirements and user stories for this module.

## Location

This module will run continuously in backend, on a server.

## Module Attributes

Attribute | Applies | Explanation
--- | --- | ---
Safety Critical | Yes | 1) Overscheduling pads or routes could theoretically produce an unsafe operating environment for aircraft.<br>2) Emergency operations take precedence.
Realtime | No | This process creates and modifies flight plans. This happens generally in advance of the flight on no strict time requirement.<br>The module does not govern flight paths, nor it does not communicate with any vehicle. A vehicle is not locked to a flight plan, pilot's responsibility.

## Global Variables

**Statically Allocated Queues**

TODO define arrays for the below

Store in memory:
- Queued low-priority flight plan requests
- Queued high-priority flight plan requests
- Main buffer for requests currently being calculated

Why:
- High priority requests are sent to their own queue and dealt with first, no need to search or sort out these requests.
- Storing requests allows batching for ride matching, etc.
- Flight requests are assigned a unique ID and an expiration timestamp.
- Clients will confirm flight plans by ID rather than by resubmitting details. This is more secure; the scheduler will only confirm flight plans that it itself has calculated.

TODO Evaluate if this is the correct route

## Logic 

### Initialization

This module performs the following at initialization:
+ Clears request queues (HIGH and LOW priority queues)
+ Clears main buffer
+ Request aircraft and pad information from storage, based on region

### Control Loop

This module will perform the following tasks at 0.2 Hz (every 5 seconds) (FIXME):
+ Read in N entries from the HIGH and LOW priority request buffers, add to main buffer with a timestamp
  + N depends on how quickly we're servicing others
+ Calculate flight plans that best accommodate the requests in the main buffer, given the state of flight plans that are already locked in
+ Offer/Update these to requesters (customers)

Every five seconds, the next grouping's requests are added to the main buffer, and flights are recalculated.

The customer is not shown the exact aircraft, pilot, etc. This allows us to swap these attributes under the hood if better solutions become available. The change will only be noticeable to the customer if the new aircraft has a slightly different departure time.

After M minutes without a resolution (selection by a customer), a request is dropped.

### Cleanup

In the event of a shutdown, clear the request queues and issue an error message to any requester.

## Interfaces

For a refresher of the processes in the Arrow backend, please see the [top level README.md](../README.md).

```mermaid
graph LR
subgraph Backend Domain
    subgraph Scheduler Module
        sch((Control Loop))
        rest(REST API)
    end
    up[Upkeep]
    pay[Payment]
    storage[Storage]

    rest -.- sch

    up -- Schedule aircraft<br/>or components<br/>for maintenance --> sch

    %% storage -- Get Flight Plans --> sch %% Maybe not necessary
    sch -- Write Flight Plans --> storage

    sch -- Confirm Payment --> pay
    pay -- Provide Confirmation --> sch
end

subgraph Client Domain
    ride(Rideshare Apps<br/>Expedia, etc.<br/>Flight Tracker)

    ride -- Make Requests --> rest
    rest -- Provide Info --> ride
end

subgraph Vehicle Domain
    air((Aircraft))
end
```

The `scheduler` does **not** communicate with the Vehicle domain.

The `scheduler` exposes a REST API to accept requests from rideshare applications and travel booking websites (Expedia, Kayak, etc.).

The `scheduler` writes flight plans to `storage`, and requests flight plans from `storage` in the event of a reboot.

The `upkeep` process gets different permissions and capabilities and communicates with the `scheduler` directly over IPC. It can send aircraft to specific maintenance facilities with HIGH priority.

### REST API

The scheduler will expose a REST API for performing the following actions:
- Requesting potential flight plans given `(time, origin, destination)`
- Confirming a new flight plan
- Requesting modifications to an existing flight plan
- Confirming modifications to an existing flight plan

A REST API allows client rideshare apps and automated Arrow protocols (such as maintenance scheduling) to make requests to the scheduler.

**Header Format Example**
```
{
    "Authorization": "Bearer RmT512bzRM430zqMLgV3Ia" // oAuth 2.0
}
```

Need proper credentials to make modifications to existing flight plan, or for specific user. Prevent another individual from making a flight plan for you unless you authorized them to.

**Requesting Flight Plans**

Request body format:
```
{
    "time": <DateTime>, // Desired departure date & time
    "origin_id": <U32>, // Desired pad of departure (by ID)
    "destination_id": <U32>, // Desired arrival pad (by ID)
    "tolerance_time_minutes": <U16>, // Allow inexact matches for departure time
    "allow_nearby_origin": <bool>, // Allow inexact matches for departure pad
    "allow_nearby_destination": <bool>, // Allow inexact matches for destination pad
    "passengers": <U8>, // Number of passengers
    "wheelchair_accessible": <bool>, // If flight needs to be wheelchair accessible
    "emergency": <bool>, // If flight needs to be high priority
}
```

This request, if successful, should add a flight request to the queue.

An example successful reply:
```
{
    "result": "success",
    "drafts": [
        {
            "draft_plan_id": 0xABCDABCD,
            "departure_time": "2022-06-10T13:30:00GMT",
            "origin_id": 0x12345678,
            "destination_id": 0x12344321,
            "npassengers": 1,
            "customer_id": 0xA4400414,
            "wheelchair_accessible": false,
            "emergency": false
        },
        {
            // ...etc
        }
    ]
}
```

**Confirming a Flight Plan**

The request:
```
{
    "draft_plan_id": <U32> // One of the draft plan IDs from a previous step
}
```

**Requesting Modifications to a Published Flight Plan**

The request:
```
{
    "flight_plan_id": <U32>, // ID of an existing flight plan

    // Any fields that the client wants change, for example destination_id
    "destination_id": <U32>
}
```

This request should be *rejected* if:
- The flight plan does not exist (for example, a draft flight plan ID is used).

An example reply:
```
{
    "result": "success",
    "flight_plan_id": 0xABCDABCD,
    "alternatives": [
        {
            "alternate_id": 0xABCDABCD,
            "departure_time": "2022-06-10T13:30:00GMT",
            "origin_id": 0x12345678,
            "destination_id": 0x12344321,
            "npassengers": 1,
            "customer_id": 0xA4400414,
            "wheelchair_accessible": false,
            "emergency": false
        },
        {
            // ...etc
        }
    ]
}
```

**Submitting Modifications to an Existing Flight Plan**

The request:
```
{
    "flight_plan_id": <U32>, // ID of an existing flight plan
    "alternate_id": <U32>, // ID of altered plan
}
```

## Tests

FIXME

### Unit Tests

FIXME