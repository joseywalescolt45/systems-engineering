# Protocols

## Flight Operations

These modules live in the Arrow backend.

Module | Description
--- | ---
[Scheduler](./scheduler) | Ride matching, flight plan calculation, APIs for client Apps, pricing based on distance and wait times
[Compliance](./compliance) |  Automate flight plan submission and flight release/dispatch approval by local airspace authority.<Br>Differs region to region based on pad of origin and destination.
[Flight Ops](./flightops) | Communicates with each aircraft, logs received telemetry.
[Guidance](./guidance) | Calculate suggested flight paths. Depends on FAA ruling of "Air Corridors" or VFR flight
[Payment](./payment) | Fiat & crypto payments, Arrow token operations
[Upkeep](./upkeep) | 1) Make requests to Scheduler for flights to maintenance facilities, in the case of aircraft or components due for replacement or servicing.<br>2) Authorize/deauthorize aircraft, components, pilots, passengers based on necessary certifications. Alert pertinent individuals if certification is expiring or has expired.


## Agenda

Discuss logical separation of protocols
Roles and responsibilities of each protocol
Prioritization of development

