# 15 — Real-time performance, resource isolation and graceful load shedding (v0.2 DRAFT)

## Principle
A system that is fast *on average* may be dangerous at its worst-case tail latency. DRONE must report **when data are too old to trust** and must never let AI inference, video processing, logs or cloud conversations delay core aircraft protection.

Flight-controller stabilization/actuation are handled by the independent flight stack. Companion-computer intelligence is not assigned hard flight-control deadlines until proven by hardware and a safety analysis.

## End-to-end observation latency
`T_total = T_exposure + T_transport + T_decode + T_inference + T_association + T_forecast + T_policy + T_delivery`.

Also track **age of information** from exposure time, not merely time since a message was received. A frame can arrive “now” but depict an earlier scene.

At 70 mph a car travels about **31.3 m/s**; a two-second stale observation corresponds to about 63 m of car travel, even before considering target motion. Use this as a warning against arbitrary TTLs, not a universal safe-alert cutoff.

| Path | Importance | Preliminary engineering policy |
|---|---|---|
| Flight stabilization / motor output | flight-critical | in autopilot; not routed through copilot, cloud or traffic AI |
| Autopilot failsafes, RC override, geofence | flight-critical | independent controller; verified firmware-specific logic |
| Telemetry/mission ACK and operator control | high | bounded queue, actual-state confirmation, lost-link handling |
| Video capture / object detection | medium for observation, high for validity of claimed alerts | time-synchronized, deadline-aware; expired frames explicitly dropped |
| Multi-target tracking / road prediction | advisory | use uncertainty and expired-track states; may downsample |
| Operator and driver alerts | advisory / human factors | short, tagged as observed vs inferred; never action command |
| Conversation / summaries / cloud APIs | optional | lowest priority; timeouts must not stall operational services |
| Logs / video archival | optional | bounded rate/storage; may drop rather than block essential work |

## Scheduling and overload policy
- Separate flight control hardware/process/communications from application inference wherever feasible.
- Reserve CPU/GPU, RAM, power and thermal budget for telemetry health, clock sync and critical alert invalidation.
- Use bounded buffers for streaming frames; when overloaded, **drop old image frames** instead of processing an ever-growing stale queue.
- Track queue depth, maximum processing age, 95th/99th percentile latency, memory pressure, GPU temperature, sensor disconnect and disk exhaustion.
- Watchdog every process, but restarting a process is not equivalent to flight-safe recovery. A restarted service must revalidate current mission and telemetry.
- Stop or degrade expensive modules (zoom search, secondary classification, logging, LLM) before compromising required timing/health checks.
- Runtime supervision should record reason-coded state changes: FULL / DEGRADED / UNKNOWN / OFFLINE.
- Keep telemetry and video failure modes separate: video outage should withdraw camera-derived traffic reports; telemetry loss is handled according to independently configured flight protocol and RPIC procedure.

## Performance budget to fill after platform selection
| Measure | Target source | Status |
|---|---|---|
| Vehicle state maximum usable age | car telemetry quality and prediction model | TBD |
| Camera frame-to-detection age | measured frame source and CPU/GPU path | TBD |
| Track confidence/expiry under occlusion | dataset coverage and motion model | TBD |
| End-to-end driver alert delay | lead-time and human-factors study | TBD |
| Planner recomputation latency | route scale, terrain, energy / legal constraint complexity | TBD |
| Operator command ACK timeout | selected MAVLink/API and firmware | TBD |
| Thermal throttling trigger | compute/airframe datasheets + chamber tests | TBD |
| Minimum flight-control failover timing | pinned autopilot firmware + HIL/flight safety case | TBD |

TBD is deliberate: numerical promises without aircraft/compute/camera characterization would be false precision.

## Power and thermal coexistence
Onboard AI draws energy and adds mass/heat. Include cameras, gimbal, radio, flight computer, cooling and reserve into aircraft power curves; a 2-hour airframe specification **without intended payload and compute is not an operational endurance claim**.

## Reliability exercises
- Load test 4K video + zoom + object tracking + telemetry + UI + LLM outage.
- Throttle GPU to simulate desert heat; ensure stale alerts withdraw.
- Simulate clock step, dropped frames, duplicate telemetry, increasing radio latency, disk full, memory leak and process crash.
- Ensure an unavailable sensor or overloaded planner never produces an apparently fresh “safe road” outcome.

Reference: PX4 offboard liveness/timeout behavior https://docs.px4.io/main/en/flight_modes/offboard . Real system shall prefer verified flight-controller mission/mode semantics over unbounded setpoint stream control.
