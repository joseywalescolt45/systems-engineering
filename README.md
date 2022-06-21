![Arrow Banner](https://github.com/Arrow-air/.github/raw/main/profile/assets/arrow_v2_twitter-banner_neu.png)

# Protocols

## Backend Protocols

These services live in the Arrow backend.

Service | Stage | Description
--- | --- | ---
[`scheduler`](./scheduler) | Concept | Ride matching, flight plan calculation, APIs for client Apps, pricing based on distance and wait times
[`storage`](./storage) | Concept |  Handles requests to update or retrieve records. Does so in batch where advantageous. Abstraction interface for potentially different storage solutions, including local storage (for simulations).
[`payment`](./payment) | | Fiat & crypto payments, Arrow token operations
[`flightops`](./flightops) | | Communicates with each aircraft, logs received telemetry.
[`guidance`](./guidance) | | Calculate suggested flight paths. Depends on FAA ruling of "Air Corridors" or VFR flight
[`compliance`](./compliance) | | Automate flight plan submission and flight release/dispatch approval by local airspace authority.<Br>Differs region to region based on pad of origin and destination.
[`upkeep`](./upkeep) | | 1) Make requests to `scheduler` for flights to maintenance facilities, in the case of aircraft or components due for replacement or servicing.<br>2) Authorize/deauthorize aircraft, components, pilots, passengers based on necessary certifications. Alert pertinent individuals if certification is expiring or has expired.

### Tools

Tool | Stage | Description
--- | --- | ---
[`simulation`](./test/simulation) | Concept | Simulates customers, aircraft, pads.<br/>Test the efficiency of route optimizations.<br/>Discover unhandled edge cases.<br/>Proof of concept without physical assets.
[`playback`](./test/playback) | Concept | Visualizer companion to `simulation`.<br/>Adjust speed, colors, zoom, etc.

## Planned Development

### Phase One **(CURRENT)**

Metadata | Description
--- | ---
Objective | Demonstrate vehicle routing given a set of known vertiport pads and vehicles.<br/>The `scheduler` will interact with the record store via the `storage` service.<br/>The `storage` service abstracts access to a cloud storage provider or local storage (in the case of simulations).
Introduces | Services:<br/>`scheduler`<br/>`storage`<br/><br/>Utilities:<br/>`simulation`<br/>`playback`
Expands |
Gantt | [Phase One Gantt - Google Drive (In Progress)](https://docs.google.com/spreadsheets/d/1n2YXbq1wimU18PORQtSU--8hPNuETFIXwcqq1udSDQI/edit?usp=sharing)

### Phase Two

Metadata | Description
--- | ---
Objective | Booking a flight through the Arrow protocol<br/>Sign in, query a flight, select a route, pay<br/>Store passenger flight history
Introduces | `payment`
Expands | `simulation`<br/>`playback`<br/>`storage`
Gantt | TBD

### Phase Three

Metadata | Description
--- | ---
Objective | Record "live" flight telemetry.<br/>Offer flight paths to "live" aircraft.<br/>Prove in Simulation.
Introduces | `flightops`<br/>`guidance`
Expands | `simulation`<br/>`playback`<br/>`storage`
Gantt | TBD

### Phase Four

Metadata | Description
--- | ---
Objective | Integration with real aircraft fleet(s), field testing.
Introduces |
Expands | `simulation`<br/>`playback`<br/>`storage`<br/>`flightops`
Gantt | TBD

### Phase Five

Metadata | Description
--- | ---
Objective | Compliance with civil aviation authorities<br/>Automatic maintenance scheduling<br/>Certification monitor
Introduces | `compliance`<br/>`upkeep`
Expands | `simulation`<br/>`playback`<br/>`storage`
Gantt | TBD

### Phase 6 and Beyond

TBD

## Other Potential Services

Roadmap Item | Description
--- | ---
[Travel Agent]() | Get to your pad from your house, auto schedules rideshare vehicles to the pad. Rerouted flights will autoschedule a ground rideshare to take you the rest of the way to the original destination. Same for Cargo - schedule a last mile cargo van.
[Personnel Routing]() | Requests HIGH priority trips for pilots, engineers, vertiport staff, etc. to get to and from their workplace (vertiport, maintenance facility, etc.) with minimal deadhead