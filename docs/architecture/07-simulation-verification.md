# 07 — Simulation, verification and test strategy (v0.1 DRAFT)

## Test pyramid
1. **Pure model tests:** coordinate transforms, route geometry, visibility/occlusion, sensor pixel projection, relative motion, energy, uncertainty propagation.
2. **Scenario simulation:** car traffic agent(s), road graph, terrain/obstacles, camera geometry, mission-state logic, replayable synthetic observations.
3. **PX4 SITL + Gazebo:** simulated VTOL dynamics, transitions, geofences, mission execution, flight-controller loss-link and mode behavior.
4. **ArduPilot SITL (adapter-conformance stage):** optional ArduPlane QuadPlane and alternate MAVLink behavior.
5. **Hardware-in-the-loop:** real flight controller, timing, radios and custom computer components while aircraft restrained / in appropriate test setup.
6. **Controlled outdoor flight:** authorized location/crew, incremental envelope expansion, logged conservative thresholds.
7. **Operational validation:** separate milestone requiring legal approvals and safety case; passing simulation never grants permission to fly on public roads.

Official starting points:
- https://docs.px4.io/main/en/sim_gazebo_gz/vehicles
- https://docs.px4.io/main/en/simulation/
- https://docs.ardupilot.org/dev/docs/using-sitl-for-ardupilot-testing.html

Choose a **pinned, tested PX4 + Gazebo release combination** during implementation; do not assume the latest docs imply all platforms/models run in every environment. Prefer Ubuntu / WSL2 where supported. Do not base the new system on deprecated Gazebo Classic by default.

## Scenario matrix
| ID | Scenario | Expected properties |
|---|---|---|
| T-001 | Road straight, car faster than aircraft | forecast growing gap and coverage expiry |
| T-002 | Switchback with valid aerial shortcut | shorter air route while preserving legal/safe flight corridor |
| T-003 | Switchback with ridge obstruction | report road section unobservable |
| T-004 | Urban road crossing crowded ground area | refuse prohibited ground-risk corridor |
| T-005 | Car stops, accelerates, turns | recompute intervals, invalidate stale assumptions |
| T-006 | Headwind shifts midflight | recalculate reserve; initiate approved contingency if needed |
| T-007 | GNSS jumps by 500 m | detect position-integrity failure; never optimize from untrusted jump |
| T-008 | Video disappears, telemetry continues | withdraw vision-based alerts; preserve independent flight control |
| T-009 | Telemetry disappears, video remains | onboard controller executes configured failsafe; no assumed control |
| T-010 | API / network failure | LLM absent without altering safety or mission authority |
| T-011 | Unknown geofence / stale airspace record | reject real flight and label simulation assumption |
| T-012 | Recover site blocked | use preapproved alternative if reachable, else escalate/contingency |
| T-013 | False negative: oncoming car missed | no message of “road clear,” no overtaking command |
| T-014 | False positive: animal | uncertainty-aware limited alert; no hard vehicle command |
| T-015 | Camera narrow-FOV zoom | degraded broad coverage appears explicitly |
| T-016 | Target at far range < required projected pixels | refuse confident detection assertion |
| T-017 | Fixed-wing stalls / impossible hover | planner rejects infeasible velocity/mode |
| T-018 | Unauthorized operator intent / replay attack | reject command at contract boundary |
| T-019 | Failed partial mission upload | validate vehicle's accepted mission and revert to safe plan |
| T-020 | Contradictory map/observed markings | flag conflict; no legal passing-zone assertion |
| T-021 | Ground truth outside visual range | no confident tracking on hidden traffic |
| T-022 | Missed heartbeat/offboard liveness | confirm actual flight-controller offboard-loss state |
| T-023 | Clock drift and out-of-order frames | expire/reject stale sensor data |
| T-024 | No authorized corridor | successful outcome is 'no mission' |

## Evidence requirements
For every test record scenario ID, software/model/aircraft version, dataset source, random seed, assumed atmospheric/flight parameters, flight/vehicle truth trajectory, observations, proposed decisions, safety-veto reasons, latency and results. Store only synthetic or privacy-reviewed artifacts.

## Key metrics
- **Coverage:** fraction of *relevant future road* that is both in unobstructed view and resolvable at target class threshold; separate geometric from perception coverage.
- **Quality:** detection precision/recall, tracking/calibration quality, forecast interval coverage and harmful false-certainty events.
- **Trajectory:** legal airspace constraint violations (must be zero in acceptance scenarios), envelope violations (zero), contingency reachability and planned-vs-real energy.
- **Reliability:** link failure response, mode/ACK correctness, time synchronization, crash/restart replay and decision explainability.
- **Human factors:** early warnings without increasing driver distraction or encouraging risky overtakes.

## Verification gates
- At G1 run every pure-model and scenario test with no real aircraft link.
- At G2 demonstrate actual flight-stack failsafe responses in SITL, not only adapter mocks.
- At G3 obtain statistically meaningful datasets for each supported object/lighting/range scenario. A realistic reliable operating envelope may be much narrower than advertised camera range.
- At G4–G5 obtain qualified independent safety review and site-specific test plans before live tests.
- Treat unknown test coverage as unverified, not pass.
