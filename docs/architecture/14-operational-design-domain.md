# 14 — Operational design domain (ODD) and operational concept (v0.2 DRAFT)

The ODD defines **when the system may operate and what it must decline**. Even perfect software is not a reason to attempt an unrestricted mission.

## Four distinct modes
**ODD-0 DESKTOP** — offline replay/math/simulated flight and synthetic perception. Open for immediate development after architecture signoff.

**ODD-1 CONTROLLED-SITE VLOS** — qualified crew, approved land/site and airspace, ground exclusion zones, tested airframe, validated local weather, manual takeover, preplanned recovery. Not yet authorized or tested.

**ODD-2 CONTROLLED TRAFFIC TEST** — closed-access test route with consenting participants, permission, ground-risk management, qualified crew and incident plan. Separate gate; not equivalent to public roads.

**ODD-3 PUBLIC-ROAD LONG-RANGE OBSERVATION** — future research scenario, **not assumed legal or technically feasible**. Must separately demonstrate VLOS/BVLOS authorization as applicable, airspace/overflight permissions, detect-and-avoid strategy, ground-risk control and validated operations; none currently available.

## ODD dimensions (must specify allowed and excluded ranges)
| Dimension | Allowed envelope definition | Current state |
|---|---|---|
| Location | latitude/longitude boundary, launch/landing area, airspace status, special-use zones | OPEN |
| Crew | designated RPIC, visual observer(s), communications, no RPIC driving | OPEN |
| Aircraft | exact firmware, payload, airworthiness/test status, Remote ID configuration, mass | OPEN |
| Weather | sustained/gust wind, precipitation, temperature, visibility, cloud ceilings, density altitude | OPEN |
| Terrain | clearance and 3D obstacle knowledge, powerlines, ridges, trees, escape options | OPEN |
| Population | below-flight people/vehicle density and third-party exposure | OPEN |
| Traffic | whether path crosses roads and which permissions/category applies | OPEN |
| Aircraft traffic | airspace class, nearby airports/helicopters, crew DAA procedure | OPEN |
| Day/night | lighting, visibility and approved configuration | OPEN |
| Communications | pilot link, command/telemetry, video link, terrain shadow and failover | OPEN |
| Navigation | GNSS quality, magnetometer interference, time sync, base-map provenance | OPEN |
| Emergency | reachable and approved landing sites, safe RPIC access and incident procedure | OPEN |
| Driver UI | safe non-directive voice alerts, passenger oversight where possible | OPEN |

## Preflight gate is a compound decision, never a single checkbox
1. Identify actual operational purpose and governing aviation framework.
2. Check airspace authorization, restrictions and active notices with authoritative source at mission time.
3. Verify crew credentials, independence of driver/RPIC roles and communications plan.
4. Inspect airframe, firmware, battery, CG, control link, Remote ID requirements and documented maintenance status.
5. Confirm site permissions, bystander exclusion, launch/landing zones and off-route recovery plans.
6. Confirm weather, visibility, wind, terrain and aircraft traffic suitability.
7. Validate map/terrain/camera calibration freshness, GNSS integrity, clock, thermal health and storage.
8. Validate flight plan, reserves, boundaries and geofence/mission-readback.
9. Require explicit RPIC go/no-go.
10. Log evidence and a safe abort/incident procedure.

### No-go is a successful safety outcome
Unknown authorization, no suitable landing site, uncertain overhead power lines, unverified winds, unmeasurable target resolution, outdated geofence, insufficient margin, unavailable RPIC, or inability to keep required VLOS => **do not fly**, even if the user requests it.

## Road driver/RPIC interaction
The car driver is never assumed to supervise an aircraft while driving. Driver alerts are low-distraction and non-directive, with silence and uncertainty explicitly handled. Flight operation is separately supervised by a capable RPIC. Telemetry for driving speed/route is an input hypothesis and can never be treated as permission to navigate over people or vehicles.

## Legal references (review currency before each real mission)
- FAA Part 107: https://www.faa.gov/newsroom/small-unmanned-aircraft-systems-uas-regulations-part-107
- FAA operations over moving vehicles: https://www.faa.gov/uas/commercial_operators/operations_over_people
- FAA airspace approvals: https://www.faa.gov/uas/commercial_operators/part_107_airspace_authorizations
- FAA Remote ID: https://www.faa.gov/uas/getting_started/remote_id
- FAA Part 107 advisory guidance AC 107-2A: https://www.faa.gov/documentLibrary/media/Advisory_Circular/AC_107-2A.pdf

Draft references are guidance and must be checked again when planning real operations; this document is not legal advice or operational authorization.
