![Arrow Banner](https://github.com/Arrow-air/.github/raw/main/profile/assets/arrow_v2_twitter-banner_neu.png)

# Scheduler - Software Design Document (SDD)

## Overview

This document details the software implementation of the aircraft scheduler.

This process is responsible for creating and modifying flight plans.

Attribute | Description
--- | ---
Status | Draft

## Related Documents

Name | Description
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
- Draft flight plans
- Alternative flight plans (for flight plan modification requests)

Why:
- High priority requests are sent to their own queue and dealt with first, no need to search or sort out these requests.
- Storing requests allows batching for ride matching, etc.
- Draft flight plans are calculated and stored in local memory as the client decides which flight they'd like to take. Each has a unique `draft_plan_id` and an expiration time (defaulting to 10 minutes).
- Clients will confirm flight plans by ID rather than by resubmitting details. This is more secure; the scheduler will only confirm flight plans that it itself has calculated.

TODO Evaluate if this is the correct route

## REST API

The scheduler will expose a REST API for performing the following actions:
- Requesting potential flight plans given `(time, origin, destination)`
- Confirming a new flight plan
- Requesting modifications to an existing flight plan
- Confirming modifications to an existing flight plan

A REST API allows client rideshare apps and automated Arrow protocols (such as maintenance scheduling) to make requests to the scheduler.

TODO Need proper credentials to make modifications to existing flight plan, or for specific user. Prevent another individual from making a flight plan for you unless you authorized them to.

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

## Ride Matching

The module will batch requests every TODO seconds when calculating new flight plans.

These flight plans will be calculated together when determining the net lowest average wait time for passengers.

TODO This is where the secret sauce occurs, lots of iteration on this algorithm

## Cleanup

Every TODO seconds the module will eliminate draft flight plans from the queue if the expiration date for each is less than the current DateTime.