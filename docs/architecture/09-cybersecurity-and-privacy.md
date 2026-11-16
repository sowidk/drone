# 09 — Cybersecurity, privacy and operational integrity (v0.1 DRAFT)

## Threat assumptions
Potential attackers, defective components or unreliable networks may inject false telemetry, spoof GNSS, replay commands, supply malicious map/route data, compromise an API token, or induce the language model to ignore constraints. A drone camera can incidentally capture people, private property, registration plates and location history.

## Trust boundaries
1. Flight-controller/autopilot is highest availability boundary; network and companion computer are untrusted by default.
2. Safety supervisor accepts only explicit versioned input from authenticated, authorized modules; missing data leads to denial/contingency.
3. Pilot/operator commands require separate authentication and role controls from driver-client read-only access.
4. LLM is a read-only explanatory service; its text output is never parsed as flight authorization or a low-level MAVLink command.
5. Public maps, camera video and external model output are **data**, never executable policy or privileged instructions.

## Controls
- Strong device identity and authenticated transport when hardware allows; prefer MAVLink 2 signing where relevant, noting signing is not confidentiality/encryption by itself.
- Keep flight-command interfaces on isolated networks/processes with allowlisted commands and independent emergency controls.
- Sign/verify mission configurations, geofence policies, software artifacts and calibration versions; log source/version.
- Enforce least-privilege API keys and local secret injection; **no keys, tokens, GPS history, or private video in GitHub**.
- Rate-limit, de-duplicate and expire commands; match ACK to request ID; re-read actual flight-controller mode after ACK.
- Defend against GNSS integrity loss, impossible velocity changes, timebase drift, sensor disagreement and map tampering.
- Define update/rollback plan for firmware, vehicle capability parameters and neural models, with safe default when incompatible.
- Disable remote administration from the driver UI; no unsafe remote code execution or operator-bypass commands.

## Privacy controls
- Default to **local processing**, event summaries and minimal retained metadata.
- Raw imagery/video: disabled from cloud upload by default; local temporary retention configurable and encrypted if enabled.
- Minimize incidental faces/plates; apply de-identification where feasible in review datasets.
- Separate synthetic simulation logs from recordings of real roads; never publish raw location/video without rights, consent and a privacy review.
- Clear UI disclosure if remote AI explanations transmit any road/context metadata; retain a full offline non-AI operation mode.
- Delete test datasets and camera calibration captures per documented retention schedule rather than unlimited storage.

## Abuse/misuse tests
Prompt injection in map/LLM output, forged MAVLink source/commands, stale authorization, compromised car GPS, malicious synthetic video frame, radio denial, secret leakage in logs, track replay, supply-chain dependency tampering, and operator session hijack.

## Security release gate
A reviewed data-flow diagram, secrets policy, operator-role permission map, simulator-only debug interfaces (disabled in flight builds), and fault tests for each security boundary.
