# 08 — Aircraft capability contract and future custom build brief (v0.1 DRAFT)

The software is **hardware independent**, but not **physics independent**. Final aircraft selection/manufacture must satisfy the following measurable interface and performance contract. A custom manufacturer cannot meet it merely by saying “supports AI.”

## A. Required interfaces
| ID | Capability / evidence requested |
|---|---|
| AC-01 | Documented and supportable flight-control interface: MAVLink 2 / MAVSDK preferred, including acknowledgments and state reporting |
| AC-02 | Independent autopilot stabilization, geofence / RTL / land / link-loss behavior configurable and testable |
| AC-03 | Mission waypoint upload/verification, progress, pause/hold/return supported in selected flight modes |
| AC-04 | Telemetry: position/datum, velocity, airspeed if fixed-wing, flight mode, battery, energy estimate, GNSS health, fault flags |
| AC-05 | Camera video access to timestamped frames in a documented transport, with lens/calibration and camera control API |
| AC-06 | Gimbal pointing & optional optical zoom with measured latency, angle limits, stabilization and orientation reporting |
| AC-07 | Onboard/ground compute interface with isolation from critical actuator commands |
| AC-08 | Authenticated command/control link, verified safe loss-link behavior, recorded limits under terrain occlusion |
| AC-09 | Safe operation within evaluated wind, temperature, precipitation, density altitude and electromagnetic conditions |
| AC-10 | Explicit payload, battery, mass/CG, propeller protection, stowed dimensions and assembly/reliability data |

## B. Preliminary physical design targets — **not guarantees**
- **Airframe architecture:** compact efficient fixed-wing VTOL / folding-wing candidate, with efficient wing-borne cruise and independent vertical landing where legally/safely possible. Keep alternatives open if measurements favor another configuration.
- **Cruise / speed:** evaluate 25–75 mph ground-route scenarios; ability to maintain 70 mph in every condition is **not** yet required if verified observation coverage is maintained, but high-speed repositioning capability is a research priority.
- **Endurance:** 30-minute *useful mission* baseline research target including reserve, sensor payload, wind and VTOL transitions. 60–120 minutes separate stretch studies.
- **Storage:** fit securely inside an enclosed vehicle compartment when stowed; dimensions must be specified for an actual car, without assumptions about vehicle model.
- **Camera:** broad-area EO + stabilized telephoto optical zoom; thermal optional. Require calibrated pixel-density benchmarks for pedestrians, animals and cars at actual viewing ranges.
- **Propulsion:** electric-first for lower integration complexity. Fuel/hybrid only if demonstrated endurance/weight benefits justify increased safety, servicing, noise and legal complexity.
- **Mass/power:** assign only after feasibility calculations with supplier; no invented takeoff weight or battery capacity.
- **Recovery:** approved stationary launch and landing sites; roof landing on moving car explicitly excluded from first version.
- **Airframe safety:** appropriate build QA, fail-safe design, propeller/rotor risks and ground risk assessed independently; do not assume adding a parachute proves safety.

## C. Performance data suppliers must provide
1. **Power/speed curve** measured across airspeed and wind directions with actual camera/compute payload.
2. Battery usable watt-hours under temperatures, degradation and high-discharge profiles.
3. VTOL hover watts, transition energy, fixed-wing minimum controllable airspeed, maximum safe airspeed and turn-radius envelope versus load.
4. Crosswind/headwind limits, turbulence behavior, climb ceiling, density-altitude effects and flight logs.
5. Sensor latency/resolution/field-of-view calibration across zoom levels and stabilization performance at cruise speed.
6. Reliable command/video range under terrain, with failure behavior and independently tested failover.
7. Safe landing requirement: pad/clearance size, touchdown precision distribution, surface limits and abort procedure.
8. Mechanical drawings, stowed dimensions, center of gravity and documented spare-part maintenance.
9. Protocol/firmware version, documented customization rights and support lifecycle.
10. Independent aircraft/flight QA evidence and test flight records.

## D. Supplier acceptance process
**Stage 1:** interface paper review and cost/risk estimate. **Stage 2:** simulated autopilot integration with software contract. **Stage 3:** power/performance test evidence. **Stage 4:** bench and HIL verification. **Stage 5:** safe, authorized controlled flight test. **Stage 6:** requirement-by-requirement review before any wider operational claim.

A bespoke airframe is not a workaround for aviation rules. If no compact aircraft satisfies the envelope, scale back mission length/scope rather than relax safety constraints.

## E. Current reference stack decision (not purchase)
**PX4 + MAVLink/MAVSDK + current Gazebo** is the first simulation reference, with ArduPilot support left as an alternative behind contract tests. This is a software architecture choice, *not* selection of HEQ, Holybro, or any custom manufacturer.

References: https://mavlink.io/en/services/mission.html ; https://mavlink.io/en/services/heartbeat.html ; https://docs.px4.io/main/en/flight_modes/offboard
