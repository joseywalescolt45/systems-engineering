# Protocols

These modules live in the Arrow backend.

Module | Description
--- | ---
[Scheduler](./scheduler) | Ride matching, flight plan calculation, APIs for client Apps
[Maintenance](./maintenance) | Automate maintenance scheduling for aircraft or components
[Compliance](./compliance) | Automate flight plan submission and flight release/dispatch approval by local airspace authority. Differs region to region based on pad of origin and destination.
[Certification](./certification) | Authorize/deauthorize aircraft, components, pilots, passengers based on necessary certifications.<br>Alert pertinent individuals if certification authorization is expiring or has expired.
[Flight Ops](./flightops) | Communicates with each aircraft, logs received telemetry.<br>TODO Discuss: Grant permission for takeoff? Use Streamr?
[Guidance](./guidance) | Calculate suggested flight paths. Depends on FAA ruling of "Air Corridors" or VFR flight
