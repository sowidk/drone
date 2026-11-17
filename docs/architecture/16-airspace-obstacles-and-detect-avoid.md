# 16 — 3D world model, obstacles and detect-and-avoid (v0.2 DRAFT)

## Why this deserves its own subsystem
The drone observes *road traffic* but must itself avoid conflicts with **air traffic, terrain, wires, towers, people and other drones**. Detecting vehicles on roads is not a substitute for aviation collision avoidance.

## 3D layers and trust levels
| Layer | Typical inputs | Uncertainties and hazard |
|---|---|---|
| Regulated airspace | official charts/authorizations, active temporary restrictions | stale notices, airspace class transitions |
| Terrain | elevation model + vertical datum | resolution gaps, incorrect AGL/MSL mapping |
| Infrastructure | available cable/tower/building maps and onsite surveys | thin power lines frequently absent, moving crane/temporary structures |
| Vegetation | surveyed trees, seasonal growth | growth, occlusion, wind sway |
| Dynamic obstacles | aircraft, birds, other UAS, balloons | limited awareness, no universal detection range |
| People and vehicles | ground-risk map, restricted test-area observers | unexpected entry, road occupancy and emergency descent consequences |
| Weather | wind, gusts, turbulence, precipitation, visibility and thermal | local conditions differ from forecast, ridgeline wind |
| Electromagnetic | GNSS availability, command/video RF interference | terrain shadow, spoofing, jammer/interference, multi-path |

When data are unverified, treat a region as **unknown**, not obstacle-free.

## Two different perception systems
1. **Road-observation perception:** tracks cars/animals/pedestrians/debris for advisory reporting.
2. **Flight-hazard perception / separation:** monitors 3D collision hazards and aircraft integrity; any autonomous avoidance must be independently validated, bounded to approved airspace, and compatible with RPIC/flight-control authority.

Avoid intermixing the two confidence scales or using a high score from a vehicle detector to justify a hazardous aerial maneuver.

## Flight-path geometry
- Respect aircraft-specific turn radius, stall/low-speed envelope, climb/descent limits, VTOL conversion behavior and rotor wash/exclusion zones.
- Inflate obstacle boundaries using navigation uncertainty, wind drift, response delay and aircraft dimensions.
- Enforce a geofence **buffer inside the formal legal boundary**. Autopilot geofence breach response is a last line, not sufficient proactive avoidance.
- Use time-dependent occupancy for manned aircraft, moving cranes and bystanders.
- Require clearance *throughout* a planned transition; checking only waypoint coordinates is insufficient.
- Optimize sky corridors under authorization and risk restrictions, not just shortest 2D straight lines.

## Detect-and-avoid architecture
- RPIC remains primary operational safety authority within applicable rules.
- Passive ADS-B In may supply partial cooperative-aircraft awareness but **does not detect every aircraft** and is not, by itself, a complete detect-and-avoid capability.
- Optical/acoustic/radar aids are candidate research inputs only when tested against necessary detection range and false-negative cases.
- No claim that autonomous software can perform BVLOS operations without required approval and demonstrated mitigation.
- When conflicts arise, the system must have a validated response that does not redirect the aircraft into people, terrain or excluded airspace.
- Emergency responses are context-dependent; “descend immediately” can be unsafe above people/traffic/terrain.

## Detection/tracking maturity test
Before using any automated obstacle response, demonstrate sensor detection envelope and latency under challenging sun glare, clouds, dusk, birds, occlusions, low signal, high closure rates, crosswind and false positives, plus bounded uncertainty. Explicitly test objects smaller than the vision system can reliably resolve.

## Failure example
Mountain switchback: drone geometrically shortcuts two miles of road, but terrain hides its communication link, a power line crosses the path and a helicopter appears beyond a ridge. A mathematical shortest path is invalid. The correct outcome can be rejecting the shortcut — or the entire mission.

References: FAA Part 107 https://www.faa.gov/newsroom/small-unmanned-aircraft-systems-uas-regulations-part-107 ; PX4 Safety https://docs.px4.io/main/en/config/safety.
