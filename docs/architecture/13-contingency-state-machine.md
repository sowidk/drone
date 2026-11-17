# 13 — Contingencies, failure precedence and graceful degradation (v0.2 DRAFT)

## Key rule
**Return-to-launch (RTL) is not synonymous with “safe.”** It may fly across a road, be unreachable in headwind, lack valid GNSS, or require a VTOL transition the aircraft cannot perform. Hover, loiter, immediate land, glide and terminate also each create different risks.

Define contingency response by aircraft mode, location, site, people/traffic beneath, weather, remaining energy, navigation quality and RPIC instructions. All are **subject to the independently configured flight-controller failsafes**, which require model/firmware-specific verification.

## Independent authority
1. Physical flight controller executes stabilization and hard-coded/configured safety behavior.
2. RPIC retains authorized takeover/emergency authority.
3. Safety supervisor may veto intelligence mission commands and request a preapproved contingency; never overrides flight-controller failsafe or disables protections.
4. Planner may propose only; computer-vision and LLM systems have **no flight command authority**.

## Two concurrent state machines
**Aircraft safety state:** READY / FLIGHT_NORMAL / SAFETY_DEGRADED / FLIGHT_CONTROLLER_FAILSAFE / RECOVERING / LANDED / UNKNOWN. Source of truth is actual flight-controller telemetry, not planner command ACK alone.

**Observation service state:** UNAVAILABLE / COVERAGE_PLANNED / OBSERVING / PARTIAL_COVERAGE / STALE / DISCONNECTED. These are *not* aircraft safety states; aircraft may be flying normally while the observation service is unavailable.

Every UI must show both independently. Never display “drone safe” just because “road observed,” or “road clear” just because the autopilot is healthy.

## Failure-response decision table — to be refined per aircraft
| Failure | Observation service | Safety supervisor/flight response | Verification |
|---|---|---|---|
| AI API unreachable | offline/plain alerts | no flight-control change | disconnect cloud |
| Camera black / freeze / mismatch | unavailable, withdraw observations | only divert if mission requires vision; obey declared autonomy | stuck frame |
| Car GPS unavailable | lose follow-a-car coverage claim | stop replanning car interception; flight stays within approved mission envelope | car link absent |
| Operator link drops | show disconnected | flight-controller link-loss action; predetermined contingency | radio blackout |
| Battery energy margin lost | no new observation objective | request verified safe recovery; autopilot low-battery failsafes are independent | headwind + battery aging |
| GNSS integrity bad | no reliable geo-based observations | autopilot position-loss action for actual flight mode | spoof/no-fix |
| Geofence threatened | no added objectives outside corridor | anticipatory margin; autopilot breach response independent | wind and turn-radius |
| Wind exceeds tested envelope | warnings about incomplete coverage | no new objectives; select feasible approved contingency | gust field |
| Camera/companion CPU hangs | stale/unknown | hardware watchdog and independent autopilot continue | process kill |
| Unsafe maneuver/transition predicted | do not issue new plan | block/replan; aircraft mode-specific protection | stall / tilt transition |
| Recovery site occupied | coverage irrelevant | preapproved alternate only if feasible; else RPIC emergency procedure | people enter site |
| Manned aircraft intrudes | observation suspended | RPIC separation action per procedure; independent flight safety | simulated intruder |
| Multiple concurrent faults | unavailable | declared autopilot escalation/priority; no silent best-effort | combined faults |

No automated failsafe here is asserted safe without airframe+site test evidence. Example: if battery is nearly exhausted, a long return may be *worse* than a nearby preapproved landing.

## Ground-recovery regions
For each planned mission, precompute a reachable set of candidate **authorized, unoccupied, suitable landing sites** and phase-dependent feasible paths. Re-evaluate as the aircraft moves; a site is not acceptable solely because it is a GPS coordinate. Consider:
- rotor/wing clearance, landing surface, slope, vegetation, people/traffic;
- crosswind, obstacle approach, fixed-wing stall/turn radius, VTOL transition ability;
- current altitude, energy margin and uncertainty;
- allowed airspace/landowner permissions and RPIC accessibility;
- loss of GNSS or video; inability to verify site occupancy.

If no acceptable contingency set can be established, **do not launch**. Feasibility is an *operational restriction*, not something software can always solve.

## Temporal safety requirements
- Declare sensor-expiry and command-deadline policy per sensor and mode.
- A stale “safe corridor” may not be executed; re-validate plans before upload and before update.
- Hardware watchdogs protect critical process execution; do not confuse watchdog restarting software with flight safety.
- Mission upload must be transactional where protocol/firmware allow, with read-back verification and partial-upload failure behavior.
- On reboot, vehicle location/mode must be re-synchronized and new operator approval obtained for application mission decisions.

## PX4 compatibility warning
PX4 implements battery, position, geofence, RC/data-link, offboard and other failsafes; the conditions/exceptions and outcomes depend on version, airframe type and configured parameters. For example, distance-aware battery return depends on a valid remaining-time estimate, and geofence actions may engage only after a breach unless separate anticipatory margin is planned. **Never rely on a default parameter value.** Pin firmware and verify action precedence with SITL, HIL and controlled flight test evidence.

Reference: https://docs.px4.io/main/en/config/safety .
