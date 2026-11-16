# 01 — Requirements and acceptance criteria (v0.1 DRAFT)

**Status:** proposed, not validated. IDs are stable for tests and design reviews. “Shall” describes intended capability, not an achieved result.

## Primary functional requirements
| ID | Requirement | Architectural verification |
|---|---|---|
| FR-001 | Ingest a road route and distinguish known vs uncertain map segments. | Route fixture with missing/inconsistent map data |
| FR-002 | Generate aerial vantage candidates independent of road centerline. | Switchback and direct-road scenario |
| FR-003 | Exclude prohibited, unknown or unverified flight corridors from executable missions. | Geofence and stale airspace fixtures |
| FR-004 | Evaluate terrain/obstacle line-of-sight and sensor resolution for each target road segment. | Raycast + projected-pixels tests |
| FR-005 | Forecast separation between aircraft and car over time. | Mixed-speed, stop/go and detour scenarios |
| FR-006 | Estimate energy consumption with speed, wind, altitude, payload, transitions and margin. | Physics model bounds and Monte Carlo |
| FR-007 | Acquire timestamped visual/thermal frames and calibration metadata. | Simulated sensor interface contract |
| FR-008 | Detect and track observable cars, pedestrians, cyclists, animals, and obstacles with uncertainty. | Labeled synthetic and held-out datasets |
| FR-009 | Predict relative-motion encounters with uncertainty and expire stale estimates. | Braking, turning, occlusion, speed changes |
| FR-010 | Describe verified passing restrictions where map/sign/marking data are reliable; never authorize passing. | Conflicting-road-evidence test |
| FR-011 | Publish concise hazard-awareness alerts with freshness, provenance and limitations. | Distracted-driving UX evaluation |
| FR-012 | Offer optional natural-language explanation while offline-critical functions still operate. | AI/cloud blackout test |
| FR-013 | Integrate a reference PX4 VTOL simulator through a versioned aircraft adapter. | End-to-end SITL mission/replay |
| FR-014 | Expose a replaceable ArduPilot/custom-aircraft adapter contract. | Mock conformance tests |
| FR-015 | Allow RPIC/operator override and visible mission cancellation state. | Command precedence and takeover tests |
| FR-016 | Record simulation evidence sufficient to replay each decision. | Deterministic replay assertion |

## Safety and operational requirements
| ID | Requirement | Verification |
|---|---|---|
| SR-001 | RPIC and verified authorization are prerequisites for real flight; driver does not substitute. | Operational checklist and arming gate |
| SR-002 | Safety supervisor is not an LLM and is independent of AI/vendor network. | Dependency cut/fault injection |
| SR-003 | Uncertainty, occlusion, stale video or lost communications never become a “road clear” indication. | Property-based negative tests |
| SR-004 | Flight controller retains stabilization, configured geofencing and loss-link failsafe autonomously. | SITL/HITL failure tests |
| SR-005 | Invalid/expired/unauthorized plans cannot arm or command real hardware. | Signed-configuration and policy tests |
| SR-006 | Emergency land/RTL/rally selection must be evaluated for terrain, energy and ground risk. | No-safe-site scenario |
| SR-007 | Aircraft shall avoid sustained flight over moving traffic unless approved under applicable rules. | Airspace/ground-risk route rejection |
| SR-008 | Operator UI must show coverage limits, health, current mode and time since last observation. | UI and alert-underload tests |
| SR-009 | Control authority precedence: flight-controller failsafe / RPIC override > independent safety supervisor > mission planner > AI suggestions. | Adversarial conflicting-command tests |

## Performance hypotheses, **not acceptance claims**
- Evaluate a **30-minute useful observation mission** as first aircraft target; two hours as an aspirational separate design case. These are not requirements that all aircraft must satisfy.
- Evaluate mixed driving speeds around 25 / 45 / 70 mph, plus stops and unexpected detours.
- Benchmark video-to-track latency, track age, end-to-end alert delay and predicted energy against measured realistic systems; **thresholds will be assigned after sensor and compute selection**, not fabricated now.
- Require a positive, documented safe-energy margin under bounded model error for every prospective trajectory; percentage and contingency model to be validated.
- Uncertainties must be represented quantitatively or as explicit “unknown,” never hidden by a green status.

## Traceability
Each requirement maps to (1) a responsible component in 02, (2) an interface in 03, (3) a hazard in 06, and (4) a simulation test in 07. A requirement remains “open” until supporting evidence is attached.

## Release gates
- **G0 Architecture:** interfaces, roles, safety case, ground-truth strategy and test matrix reviewed.
- **G1 Deterministic desktop simulation:** scenario coverage and invariant tests.
- **G2 PX4 SITL:** command protocol and mode/failsafe validation.
- **G3 Perception evidence:** controlled dataset benchmarks, calibrated sensors and uncertainty estimates.
- **G4 Hardware-in-the-loop:** timing, power, communications and failsafe verification.
- **G5 Controlled flight:** permits, trained crew, site/risk review, conservative safety envelope.
- **G6 Wider operation:** separate regulatory approvals and operational safety demonstration.

Do not skip gates merely because the simulator passes.
