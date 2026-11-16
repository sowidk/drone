# 10 — Build sequence, repository layout and architecture decisions (v0.1 DRAFT)

## Current milestone: ARCHITECTURE ONLY
**Do not start implementation** until documents 00–11 are reviewed and open assumptions have owners and pass/fail criteria. A documentation-only branch and draft PR allow review without placing incomplete implementation into main.

## Proposed future repository layout — design only, directories not created
```text
drone/
  README.md
  docs/
    architecture/             # requirements, system design, risks, interfaces, test plan
    decisions/                # ADRs / tradeoffs (future)
    operations/               # only after authorized flight testing
  schemas/                    # versioned event and mission contracts (future)
  packages/
    mission-core/
    geo-intelligence/
    route-optimizer/
    safety-supervisor/
    aircraft-adapters/
    sensor-ingestion/
    perception/
    target-tracking/
    traffic-forecast/
    alert-engine/
    operator-console/
    driver-client/
    copilot/
  simulation/
    dynamics/
    scenes/
    synthetic-sensors/
    traffic/
    fixtures/
  tests/
    unit/
    scenarios/
    integration/
    sitl/
    hardware-in-loop/
  tools/
    validation/
  .github/
    workflows/                # later: tests, lint, security scanning
```
Shared modules should use declared contracts, not import private implementation types or bypass the safety supervisor.

## Phases and stopping points
| Phase | Output | Gate |
|---|---|---|
| A0 | Architecture baseline & documented unknowns | Approve detailed design; **no application code** |
| A1 | Schema definitions + pure mission/energy/geometry math | Tests prove units, datums, uncertainty |
| A2 | Deterministic scenario engine and car/terrain models | Scenarios T-001–T-024 reproducible |
| A3 | PX4 SITL/Gazebo VTOL aircraft adapter | Actual mission/mode/failsafe behavior demonstrated |
| A4 | Synthetic perception/tracking/forecasting | Dataset quality and uncertainty report |
| A5 | End-to-end virtual observer and read-only UI | No pass authorization, alert discipline |
| A6 | Flight-linked hardware/protocol verification | Supplier evidence + HITL + safety review |
| A7 | Authorized controlled-site tests | RPIC / operation authorization / site safety |
| A8 | Mission scope expansion | Regulatory and safety evidence, not just performance |

## Architecture decisions (ADRs)
| ID | Proposed decision | Rationale | Status |
|---|---|---|---|
| ADR-001 | Monorepo `sowidk/drone` | Traceable contracts, tests, docs and adapters | Proposed |
| ADR-002 | PX4 SITL + modern Gazebo first | Open VTOL simulator, realistic flight-stack integration | Proposed |
| ADR-003 | MAVLink 2/MAVSDK adapter, not closed proprietary SDK | Replaceable aircraft | Proposed |
| ADR-004 | Fixed-wing VTOL reference envelope | Cruise efficiency + stationary vertical launch/recovery | Proposed hypothesis |
| ADR-005 | No direct LLM flight control | Flight-critical isolation | Safety constraint |
| ADR-006 | Never produce “safe to pass” signal | Observation incomplete; driver retains responsibility | Safety constraint |
| ADR-007 | Local-first perception and privacy | Resilience, low latency, minimization | Proposed |
| ADR-008 | Flight-controller missions favored over fragile continuous offboard commanding | Survive companion/link loss | Proposed, verify in SITL |
| ADR-009 | Site-specific authorized corridors required before real flight | Airspace and ground safety | Safety constraint |

## Open decisions before A1
- Confirm exact intended operator concept: dedicated RPIC vs not; VLOS test sites; jurisdiction and operating purpose.
- Define sensor classes and minimum resolvable object/range as *test hypotheses*, after calibration.
- Define actual vehicle compartment dimensions (not assumed), aircraft transport/assembly constraints.
- Choose documented aircraft simulation airframe and pinned PX4/Gazebo/tool versions.
- Choose language and serialization once schema semantics are accepted; no need to decide language prematurely.
- Set measurable performance targets from credible datasets and prototype power/telemetry measurements.
- Agree privacy retention policy and operator UI alert vocabulary.
- Identify feasible fallback when planned aerial corridor/landing site is unavailable.

## Definition of done for architecture
All FR/SR IDs assigned; diagram/contract versions internally consistent; risk register reviewed; tests for critical invariants specified; supplier capability brief usable as an RFP; unresolved regulatory feasibility explicitly highlighted; implementation plan separated from flight authorization. Then user approval to begin coding.
