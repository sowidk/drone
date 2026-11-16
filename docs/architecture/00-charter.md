# 00 — System charter and scope (v0.1 DRAFT)

## Purpose
DRONE investigates whether an autonomous aerial observation platform can provide **advance, uncertainty-aware road-hazard awareness** to a vehicle occupant. The aircraft might cut across safe aerial corridors, occupy selected vantage points, and report slower vehicles, approaching traffic, pedestrians, cyclists, animals, debris, or stopped traffic. The mission is informational: **never infer that an unobserved road is clear; never issue a real-time “pass now” command.**

This is a personal research system, not an emergency service, traffic authority, or automotive driver-assistance certification.

## Roles and responsibilities
- **Remote pilot in command (RPIC)**, where required: operates aircraft legally, supervises the flight and can terminate/divert it. The driver cannot be assumed to be available as the RPIC.
- **Driver:** keeps control of the vehicle and verifies road conditions directly; receives optional non-distracting alerts.
- **Independent flight controller:** aircraft stabilization, low-level controls and configured failsafes, even when intelligence software is offline.
- **Safety supervisor:** deterministic admission checks; rejects or terminates unsafe observation plans and refuses invalid controls.
- **Mission planner:** proposes observation waypoints and energy-aware trajectories, subject to independent safety approval.
- **Perception/tracking:** probabilistic estimates of observed objects, with timestamps, uncertainty and limitations.
- **AI conversation layer:** optional explanation and natural language, not a flight-control or safety-critical authority.

## In scope for software-first R&D
1. Offline road/terrain/airspace planning with source freshness and uncertainty.
2. Optimal observation-position planning instead of road-following.
3. Vehicle and hazard perception, tracking, relative-motion forecasts.
4. Local-first telemetry/event recording, operator user interface and alert policy.
5. Aircraft-neutral contracts, PX4 reference adapter and optional ArduPilot adapter.
6. Reproducible digital-twin simulation, negative/fault scenarios and safety case.

## Not in scope for initial implementation
- Public-road BVLOS flight, automatic operations above traffic, or routine vehicle escort.
- Control of vehicle steering/throttle/braking.
- A binary “safe to overtake” verdict or driving instructions based solely on aerial footage.
- Custom aircraft manufacture, physical roof launcher, autonomous recovery on moving vehicles.
- Flight-critical control through an OpenAI API or other remote language model.

## Mission modes (research, not permissions)
- **M0:** log replay and analytical desktop simulation, no aircraft.
- **M1:** flight-stack SITL with synthetic scenery and traffic.
- **M2:** authorized stationary-site VLOS observation test with qualified crew.
- **M3:** restricted test-area trials with moving ground vehicles, only if permitted.
- **M4:** longer-route research requiring separate aviation/legal feasibility demonstration; no automatic assumption of authorization.

## Architectural principles
- Prefer open standards and clear replaceable adapters.
- All critical operation requires fresh, corroborated inputs and bounded uncertainty.
- Local flight safety and geofence enforcement continue without cloud/network.
- Favor explicit state machines, audit trails, model versioning and replayable decisions.
- Public repository: commit no API credentials, raw private vehicle routes, license plates or personal location history.

## Major unknowns
Airframe speed/power curve, fixed-wing stall/loiter constraints, sensor pixel density under actual visibility, radio and video reliability in terrain, emergency recovery sites, the applicable aviation operating category, and whether a useful mission can remain within legal visual-line-of-sight and ground-risk constraints. These are **design blockers**, not implementation bugs.

## Exit gate for architecture
An approved requirements baseline, risk register, data contracts, hardware capability contract, simulator test matrix, and explicitly documented unresolved assumptions. No implementation code before that review.
