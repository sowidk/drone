# DRONE — Autonomous Aerial Road Observation

**Status:** Architecture design only. **No application code or flight operations authorized.**

This repository is the single home for the design, simulation, software, tests, and future aircraft integration of a personal aerial road-observation research project.

## Mission

Study whether an aircraft can improve advance awareness of road conditions and hazards by observing safe, legally authorized aerial corridors and reporting uncertain, timestamped findings to a driver. The system **does not drive**, **does not authorize overtaking**, and **does not replace the driver's judgment or a remote pilot in command**.

## Engineering rules

1. **Architecture before implementation.** Requirements, contracts, hazards, and acceptance gates are reviewed before code is written.
2. **Hardware-independent intelligence.** Simulated aircraft first; future PX4/ArduPilot/custom aircraft behind adapters.
3. **Flight-critical autonomy stays independent.** A certified/appropriate autopilot and safety supervisor govern flight. A language model may explain findings but never directly control motors or override safety constraints.
4. **Fail conservatively.** Loss of detection, video, communications, or location certainty must not be presented as a clear road.
5. **Simulation before field testing.** Real flights require a qualified remote pilot, safe test environment, and applicable FAA authorization.
6. **No secrets or sensitive location history committed.** This is a public repository.

## Immediate milestone

Create and review architecture documents in a documentation-only pull request. **No code until this milestone is signed off.**

## Reference documentation

See the draft design under `docs/architecture/` after the architecture PR is opened. The current documents describe design targets, not validated flight capabilities.
