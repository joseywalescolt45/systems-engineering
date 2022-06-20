# Protocols

## Backend Protocols

These services live in the Arrow backend.

Module | Description
--- | ---
[Scheduler](./scheduler) | Ride matching, flight plan calculation, APIs for client Apps, pricing based on distance and wait times
[Storage](./storage) | Handles requests to update or retrieve records. Does so in batch where advantageous. Abstraction interface for potentially different storage solutions, including local storage (for simulations).
[Payment](./payment) | Fiat & crypto payments, Arrow token operations
[Flight Ops](./flightops) | Communicates with each aircraft, logs received telemetry.
[Upkeep](./upkeep) | 1) Make requests to Scheduler for flights to maintenance facilities, in the case of aircraft or components due for replacement or servicing.<br>2) Authorize/deauthorize aircraft, components, pilots, passengers based on necessary certifications. Alert pertinent individuals if certification is expiring or has expired.
[Guidance](./guidance) | Calculate suggested flight paths. Depends on FAA ruling of "Air Corridors" or VFR flight
[Compliance](./compliance) |  Automate flight plan submission and flight release/dispatch approval by local airspace authority.<Br>Differs region to region based on pad of origin and destination.

## Roadmap

Roadmap Item | Description
--- | ---
[Travel Agent]() | Get to your pad from your house, auto schedules rideshare vehicles to the pad. Rerouted flights will autoschedule a ground rideshare to take you the rest of the way to the original destination. Same for Cargo - schedule a last mile cargo van.