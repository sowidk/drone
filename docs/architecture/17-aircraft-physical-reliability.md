# 17 — Airframe, powertrain and physical reliability architecture (v0.2 DRAFT)

## Responsibility split
Software can estimate a maneuver's feasibility and reject commands, but cannot manufacture lift, maintain thrust after certain physical failures, or prevent every crash. A future aerospace partner must provide a **measured flight envelope** and a defensible airworthiness/test case for the custom aircraft.

## Candidate architecture
Compact, safely transported **fixed-wing VTOL** remains the preferred analysis concept because wing-borne cruise may be more efficient than multirotor hover. It is a *hypothesis* until measurements show a mass/speed/endurance/storage advantage. A folding or detachable wing introduces mechanical locks, fatigue and assembly error hazards. For any configuration, evaluate the actual passenger vehicle's enclosed storage, not an assumed car model.

## Core hardware groups and verification
| Group | Primary concerns | Supplier evidence |
|---|---|---|
| Propulsion and flight surfaces | motor/ESC/prop failure, stall, rotor strike, transition failure | thrust maps, thermal cycling, single-failure tests, actuator logs |
| Airframe/assembly | structural limits, fatigue, fold-joint locking, transport damage | load cases, inspection intervals, mechanical drawings |
| Batteries / fuel | usable energy at current draw, heat, failure, aging, storage fire | cell/BMS records, charge/discharge curves, endurance tests |
| Position/navigation | GNSS integrity, IMU calibration, magnetometer, barometer, time sync | sensor validation, failure response |
| Flight controller | verified firmware, actuator interfaces, failsafe settings, manual takeover | HIL/SITL logs, signed build inventory, configuration |
| Companion AI compute | compute performance, GPU heat, power and isolation | power/thermal under full video load |
| Communication links | telemetry, video, control, antenna obscuration, failover | range/latency/performance under terrain shadow |
| Cameras/gimbals | zoom latency, stabilization, vehicle/animal projected pixels, exposure | calibrated test footage by range/weather |
| Ground recovery | surface, rotor/wing clearance, civilians nearby, landing accuracy | landing tests and site standards |
| Identification and operations | registration, Remote ID applicability/configuration, markings | required regulatory documentation |

## Mission endurance equation
A verified energy budget considers flight phases:
`E_used = E_takeoff + E_transition + ∫P_cruise(v_true, wind, density, payload)dt + E_observation + E_recovery + E_computing + E_margin`.

**Do not double count** integrated accessory/propulsion loads in a measured total power curve; if the whole-aircraft test includes avionics, they should not be added again. Use measured watt-hours and uncertainty, not percentage battery alone.

The aircraft must continuously maintain ability to reach an *authorized and suitable* recovery site under flight-envelope constraints, including drift, terrain and potential blocked destination. If not possible, the mission should not begin; if margin degrades unexpectedly, trigger the preplanned contingency early.

## Redundancy and common cause
Two GPS receivers can both be spoofed; two cameras can both be blinded by fog; dual batteries can share a faulty power bus. Redundancy is useful **only if independence, detection, switching time, mass/power cost and failure consequence are demonstrated**.

Potential options for manufacturer evaluation (not mandated): separate flight-controller supply, independent receiver/backup pilot link, redundant position source, power monitoring, appropriate parachute/flight-termination equipment subject to ground-risk analysis. Do not assume any safety device is safe to deploy over moving traffic.

## Environmental tests
Desert heat, direct sun, high density altitude, cold battery discharge, dust, rain/icing exclusion, crosswinds, wind shear over ridges, bird strike and cable collision, transport vibration, mechanical fatigue, corrosion and battery degradation. State disallowed conditions when not validated.

## Acceptance
No flight beyond controlled test site until:
- Measured flight envelope and power curves include actual sensor and computer payload.
- Independent stabilization / failsafe verified for specific firmware/airframe.
- Critical mechanical and electrical failure modes assessed and inspected.
- Safe recovery and third-party ground-risk case reviewed.
- Authorized pilot, test site, permissions and procedures documented.

**Two-hour flight** is a research stretch objective, not a mass/battery guarantee. The expected safe envelope may be far smaller than the attractive marketing specification.
