# 11 — Source register, assumptions and unresolved feasibility (v0.1 DRAFT)

## Source register (verified web documentation as of 2026-11-16)
1. FAA — [Part 107 overview](https://www.faa.gov/newsroom/small-unmanned-aircraft-systems-uas-regulations-part-107). Operational restrictions, pilot responsibilities, VLOS.
2. FAA — [Part 107 waivers](https://www.faa.gov/uas/commercial_operators/part_107_waivers). Lists regulations for which waivers may be sought, including certain moving-vehicle, BVLOS and operation limitations.
3. Federal regulation — [14 CFR §107.145 operations over moving vehicles](https://www.ecfr.gov/current/title-14/chapter-I/subchapter-F/part-107/subpart-D/section-107.145). Conditional limits on sustained flight over moving vehicles.
4. PX4 — [current Gazebo vehicle models](https://docs.px4.io/main/en/sim_gazebo_gz/vehicles). Includes standard VTOL, tailsitter, tiltrotor and fixed-wing simulator models.
5. PX4 — [simulation guide](https://docs.px4.io/main/en/simulation/). SITL/HITL capabilities.
6. PX4 — [offboard mode](https://docs.px4.io/main/en/flight_modes/offboard). Liveness requirement and offboard-link-loss behavior.
7. MAVLink — [mission protocol](https://mavlink.io/en/services/mission.html). Flight plans, geofences, rally points, ACK/mission upload issues and PX4/ArduPilot differences.
8. MAVLink — [heartbeat protocol](https://mavlink.io/en/services/heartbeat.html). Connection monitoring and limitations.
9. ArduPilot — [Using SITL](https://docs.ardupilot.org/dev/docs/using-sitl-for-ardupilot-testing.html). ArduPlane/QuadPlane simulation and frame selection.

These are engineering and regulatory *reference sources*, not evidence that an aircraft configuration has been approved or proven.

## Key assumptions requiring measurement
| Assumption | What would verify it |
|---|---|
| Road observation useful from high altitude at long distance | Survey geometry, annotated sensor footage and validated target pixel thresholds |
| A compact aircraft can cover mixed urban/rural/highway journey | Airframe power/airspeed/weight data with route+wind Monte Carlo |
| Corner cutting allows slower aircraft to stay ahead of car | Terrain/airspace/camera-aware routing tests on actual road geometry |
| Optical zoom can track multiple distant hazards | Camera stabilization/slew/field-of-view benchmarks |
| Accurate vehicle speed from aerial video | Calibrated multi-frame ego-motion and ground-truth evaluation |
| 30-minute useful observation endurance | Flight-phase energy analysis including hover/transitions and safe-site reserve |
| Operator can maintain required visual contact | Verified mission geometry and RPIC procedures / approvals |
| Continuous flight near roads is lawful | Operation-specific regulatory review, authorizations, crew and risk controls |
| Custom aircraft will accept our software contracts | Supplier firmware and protocol evidence; SITL/HITL conformance |
| Hosted LLM adds useful value | Human-factors comparison while preserving independent offline safety |

## Explicitly rejected shortcuts
- Inferring flight safety from advertised maximum flight time.
- Equating camera transmission range with reliable vehicle detection range.
- Equating “legal passing zone” with a real-time safe passing decision.
- Treating 400 ft elevation as ability to see everything in any terrain.
- Assuming no object detected means no object present.
- Assuming autonomous software makes human pilot/airspace obligations disappear.
- Letting an LLM, connected phone or driver-facing app directly command flight actuators.
- Training perception solely on synthetic images and claiming real-world reliability.

## Review questions for architecture sign-off
1. Is there a useful permitted mission concept beyond static-site trials?
2. How often will 'no safe flight' or 'no visibility' occur on a target road?
3. Which alerts increase awareness without encouraging passing or distraction?
4. Can we make reproducible safety claims within simulator and dataset limitations?
5. What supplier evidence would make an actual custom airframe worth funding?
