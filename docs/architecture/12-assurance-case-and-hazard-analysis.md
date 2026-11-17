# 12 — System safety assurance case and hazard analysis (v0.2 DRAFT)

**Objective:** develop evidence-based arguments that a specific restricted operating envelope is acceptably safe. No design, simulator, vendor spec or AI model guarantees zero risk. **The safety case has no approved operational claims yet.**

## 1. Safety assurance claim structure (Goal–Argument–Evidence)
| Claim | Argument required | Evidence required | State |
|---|---|---|---|
| G-SAFE-001 No unacceptable ground-impact risk | Exposed population, aircraft energy and consequences bounded; independent prevention/mitigation | site-specific ground-risk analysis, airframe tests, emergency plans | OPEN |
| G-SAFE-002 No unacceptable airspace collision risk | Defined VLOS/crew control and collision-avoidance procedure | permits, airspace map, crew drills, traffic encounter trials | OPEN |
| G-SAFE-003 No unapproved mission execution | Invalid mission cannot be uploaded/armed/executed | safety-gate property tests, autopilot mission state verification | OPEN |
| G-SAFE-004 Single noncritical computer failure does not remove flight control | Flight controller independent of AI/companion/cloud | physical disconnect tests, link-loss tests, power-separation analysis | OPEN |
| G-SAFE-005 Misleading road advice cannot trigger passing authorization | Alerts never imply permission, sensor absence never means clear | exhaustive UI-state tests, human-factors trials | OPEN |
| G-SAFE-006 Recover/contingency remains feasible within declared envelope | Energy, sites, weather and failures jointly modeled | measured aircraft energy curves, adverse scenarios, flight data | OPEN |
| G-SAFE-007 Unknown or invalid perception degrades output conservatively | Occlusion, blindness, low confidence, staleness are explicit | held-out datasets, negative examples, uncertainty/calibration tests | OPEN |
| G-SAFE-008 Aircraft behavior is bounded under failures and mode changes | Autopilot-specific configured failsafes + tested precedence | pinned firmware params, SITL/HITL traces, emergency flight trials | OPEN |

Each argument must record **assumptions**, limitations, accountable reviewer, revision, evidence link and acceptance criteria. An OPEN claim is *not* a demonstrated safety property.

## 2. Hazard analysis process
Use complementary views, because no one technique finds every failure:
- **STPA-style unsafe-control-action review:** omission, unsafe issue, early/late command, overly long/short duration, wrong operating mode, wrong recipient; include interactions of individually healthy components.
- **FMEA/FMECA:** at component/interface level — failure mode, detectability, resulting effect, contributing conditions, severity, mitigation, diagnostic coverage.
- **Fault-tree analysis:** top events — ground impact on third parties, midair collision, hazardous driver alert, unauthorized flight, inability to recover — decompose including common-cause failures.
- **Human-factors hazard review:** driver distraction, automation bias, overconfidence, RPIC fatigue, ambiguous authority and handoff delays.
- **Cyber/physical safety interaction:** command spoof, map tampering, GNSS spoof, video corruption, adversarial target detection, loss of time sync.
- **Systematic scenario coverage:** combination of environment, traffic, aircraft mode, sensor faults, connectivity and operator state, not only isolated unit faults.

## 3. Preliminary critical hazard register extensions
| ID | Hazard / failure condition | Initial mitigation concept | Non-negotiable negative test |
|---|---|---|---|
| H-17 | Engine/propulsion loss during fixed-wing cruise | aircraft-specific glide envelope + safe destination under current winds | no reachable site => mission excluded |
| H-18 | VTOL motor or transition failure | transition-specific failure detection, appropriate mode reversion if physically possible | failure during transition, no false 'recoverable' claim |
| H-19 | Battery thermal runaway/fire during flight or storage | cell QA, BMS, physical containment/storage practice, site exclusion | heat/voltage abnormality |
| H-20 | Aircraft struck by bird, debris or wire | route exclusion, altitude/obstacle and inspection policies | unmodeled wire and nearby birds |
| H-21 | Compromised/stale weather update | independent observation, bounded wind envelope and escape contingency | wind shift + stale forecast |
| H-22 | Shared GNSS/common clock fault defeats multiple modules | validity monitors, diverse evidence where feasible, no fake redundancy | simultaneous consistent-looking bad measurements |
| H-23 | CPU/GPU/thermal overload causes delayed decisions | deadline watchdogs, degraded operating modes, priority isolation | load spike and thermal throttling |
| H-24 | Unsafe RPIC/driver role confusion | distinct consoles, named operator authority and documented handoff | driver tries to authorize flight |
| H-25 | Collision while landing near car / bystander | fixed exclusion zone, crew clearance, approved site | vehicle enters landing zone |
| H-26 | Emergency failsafe descends over occupied road | preapproved contingency regions; flight controller-specific consequences | emergency over live traffic |
| H-27 | Camera detects wrong road/parallel road | geo alignment, road-association uncertainty and road-ID controls | overpass / switchback confusion |
| H-28 | Stale predictions prompt driver to act | explicit non-directive alert policy; fast expiration | delayed alert after target turns |
| H-29 | Catastrophic software/system update in field | version pin, signing, rollback and no live-flight updates | interrupted update and incompatibility |
| H-30 | System resumes stale mission after reboot | arm/mode reset, explicit new approval, inhibit mission replay | restart at new location |
| H-31 | Ground risk from parachute or termination | independent review of deployment envelope / consequence | deployment above occupied road |
| H-32 | Real aircraft enters manned-aircraft path | RPIC, avoidance procedure, restricted envelope and emergency descent policy where safe | unexpected aircraft encounter |
| H-33 | Visual observer loses sight, aircraft continues beyond approved area | VLOS geometry/crew detection and terminate/divert plan | ridge occlusion or sunset |
| H-34 | Privacy harm from road recording | local-first minimum retention, de-identification and access control | unauthorized video access |

## 4. Risk acceptance
No numerical probability can be assigned from imagination. Record severity, estimated likelihood with evidence quality, detectability, residual risk and uncertainty; use a risk matrix only as a review aid. For high-consequence top events, require independent safeguards, independent evaluation and explicit test evidence.

Use the hierarchy: **eliminate hazardous operation** > **physically/architecturally prevent it** > **detect and mitigate** > **procedures/warnings**. If a mission creates unacceptable unavoidable third-party risk, decline the mission.

## 5. Open safety design questions
- Independent hardware safety features needed for a specific airframe and weight class?
- What is the actual consequence envelope from loss of propulsion, stall, descent, parachute or flight termination at each point in a corridor?
- Which sensor errors are common-cause, even with two receivers or two cameras?
- Which risks arise from a *perfectly functioning* mission plan that people misunderstand?
- What is the evidence threshold for each operating envelope expansion?

References: FAA AC 107-2A (risk assessment examples); NIST Autonomous Systems Assurance: https://www.nist.gov/programs-projects/autonomous-systems-assurance ; PX4 safety reference https://docs.px4.io/main/en/config/safety.
