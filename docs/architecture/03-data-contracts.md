# 03 — Data contracts and timing semantics (v0.1 DRAFT)

The following are **schema designs**, not code. Actual schema language will be chosen at the architecture approval gate. All contracts need schema_version, source_id, trace_id and validation behavior.

## Common metadata — required on every event
| Field | Type / convention | Rule |
|---|---|---|
| schema_version | semantic version | incompatible changes fail validation |
| trace_id, mission_id | opaque identifiers | no personal identifiers in logs |
| source_id | declared sensor / component identity | spoof-resistant when used for commands |
| observed_at_utc | UTC timestamp | time of observation, not receipt |
| received_at_monotonic | monotonic clock | latency and staleness, not wall time |
| valid_until | monotonic deadline / TTL | expired evidence is ineligible for safety decisions |
| confidence / uncertainty | calibrated distribution or “unknown” | not just a cosmetic score |
| provenance | model ID, sensor calibration, map dataset version | reproducible predictions |
| frame / datum | explicit coordinate reference | no implicit lat/lon, feet/meters or MSL/AGL assumptions |

## Contract inventory
| Contract | Producer > consumer | Critical content |
|---|---|---|
| VehicleState.v1 | vehicle mock / consented source > planner | time, position uncertainty, speed uncertainty, predicted route hypotheses |
| AircraftState.v1 | adapter > supervisor/planner | position, altitude datum, ground/air speed, flight mode, health, battery estimate, GPS integrity, link health |
| AircraftCapability.v1 | adapter/airframe file > planner | supported modes, stall range, turn envelope, VTOL transition limits, speed-power lookup and uncertainty |
| ObservationFrame.v1 | sensors > perception | acquisition time, camera intrinsics/extrinsics, gimbal angle, pose covariance, frame ID, video reference |
| ObjectObservation.v1 | perception > tracker | type, pixel box/mask, image score, distance method, geolocation + covariance or unknown |
| TrackedObject.v1 | tracker > forecast | stable ephemeral ID, estimated motion, uncertainty, occlusion age, source provenance |
| RoadSegmentEvidence.v1 | geo / perception > forecast | road segment, terrain visibility, legal restriction evidence, map freshness, confidence |
| TrafficForecast.v1 | forecast > alert engine | scenario assumptions, catch-up time interval, possible cross-traffic, stale time |
| HazardEvent.v1 | forecast > alert engine | observed hazard class, spatial/temporal uncertainty, observed vs inferred |
| CandidateMission.v1 | planner > supervisor | waypoints, altitudes/datum, constraints, expected observation coverage, energy estimate, alternatives |
| SafetyDecision.v1 | supervisor > adapter/operator | deny/approve/abort, rule IDs, mode, signed configuration version, decision timestamp |
| OperatorIntent.v1 | RPIC console > mission-core | manual authority identity, consent, cancellation and reason |
| AircraftCommandAck.v1 | adapter > mission-core | requested command, vehicle response, effective mode, timeout / refusal |
| DriverAlert.v1 | alert engine > car client | short advisory, freshness, location approximation, uncertainty, non-actionable wording |

## Rules for physical quantities
- Earth positions: WGS84 lat/lon with explicit degrees and altitude datum, ideally ECEF/ENU for local vectors. Terrain data source and vertical datum are mandatory.
- Dynamics: SI internally (m, m/s, seconds, joules, watts); convert mph/ft only at presentation boundaries.
- Camera: timestamps aligned to aircraft pose, intrinsic calibration, distortion profile and zoom setting; unknown calibration means no precise geolocation.
- Tracks: distinguish `observed`, `predicted`, `occluded`, `stale`, and `unavailable`; do not silently merge them.
- Battery: reported percentage is *not* energy remaining; account for voltage, discharge behavior, capacity degradation, temperature and actual consumption.
- Each interface declares tolerable latency, missing-data behavior, producer ownership and version compatibility before implementation.

## Event handling
- Use an append-only audit trail of **redacted metadata** and optional encrypted local sensor buffers with configurable short retention. No secrets or uncontrolled raw video in the public repository.
- For commands, deduplicate by request ID, require explicit ACK/actual state confirmation, reject stale/out-of-order or unauthorized intents.
- For perceptions, replay deterministically with fixed model/version/data seeds where feasible.
- Distinguish loss of **control telemetry** from loss of **video feed**. Both require different conservative behavior, neither means the road is clear.
- Never infer an actionable passing decision from RoadSegmentEvidence; it describes restrictions/evidence only.

## Interface review tests
Contract conformance, corrupted packets, out-of-order timestamps, mixed altitude datums, stale calibration, duplicate messages, clock drift, sensor disagreement, unknown vehicle capability, ambiguous target identity and missing GPS.
