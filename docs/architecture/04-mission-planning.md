# 04 — Aerial observation and energy planning (v0.1 DRAFT)

## Problem definition
The drone **does not follow the road centerline**. It searches for a connected, legally permissible flight path and observation positions that maximize visibility of future road segments at resolvable sensor detail, within aircraft flight envelope and guaranteed contingency margin.

Inputs: expected car trajectory distribution, road graph, terrain/obstacles, authorized 3D airspace, moving-vehicle ground risk, camera pointing/range, winds/forecast uncertainty, aircraft capabilities, energy model, recovery sites, current aircraft/car estimates and operator-approved mission boundaries.

Outputs: candidate vantage points, safe flight corridor graph, timed waypoints/modes, expected visibility intervals, predicted energy intervals, contingency route and explicitly **unobservable** road portions. Proposed plans are never self-authorizing.

## Geometric coverage
- Calculate actual 3D line-of-sight by terrain raycast plus modeled vegetation/building/structure layers, acknowledging missing layers.
- Compute minimum projected pixels for target classes from sensor focal length, pixel pitch/FOV, zoom, range, orientation and atmospheric visibility.
- Represent observable road as time-dependent coverage rather than simply “within X miles”.
- A drone behind the car can still observe ahead only if line of sight and image resolution remain sufficient.
- Quantify footprint loss from hills, bends, narrow FOV, camera slew limits and occlusions.
- Avoid assuming road visibility from altitude alone; 400 ft AGL is not permission to exceed legal caps or to conduct BVLOS operations.

## Route optimization
Build a 3D graph of permissible observation regions and transitions, rather than directly joining road waypoints. Each edge requires:
1. airspace/geofence eligibility and operator approval;
2. terrain, infrastructure and traffic-ground-risk clearance;
3. reachable aircraft speed/turn/climb/VTOL transition envelope;
4. time-to-coverage and predictable observation geometry;
5. worst-case bounded energy and accessible contingency site;
6. minimum command and telemetry integrity.

A multi-objective cost may be considered:
`J = w1*(unobserved future-road time) + w2*(energy) + w3*(exposure/ground risk) + w4*(latency)`.
**Safety and legality are hard constraints, never soft weights.** Avoid reward hacking: a lower energy score cannot justify entering excluded airspace or sacrificing emergency reserve.

## Moving-car prediction
- Car state includes current speed, uncertainty, route hypotheses and possibility of turns/stops. Do not assume the car follows a single GPS route.
- For simple separation: `relative gap change = (car groundspeed - drone projected groundspeed) * dt`; for real paths, integrate arc-length/time and sight-lines.
- "Cut corners" can reduce aircraft distance, but the shortcut is valid only if safe/authorized with adequate sensing.
- When road route becomes uncertain or car deviates, degrade planned coverage and replan conservatively.
- If drone cannot preserve validated visibility, report “coverage unavailable”; do not promise continuous watch.

## Endurance model
- Distinguish airborne time, **energy** and horizontal range; manufacturer endurance is rarely measured at useful wind/zoom/payload settings.
- Estimate power versus true airspeed, density, wind direction, climb, altitude, temperature, payload and propulsor efficiency.
- Model VTOL hover, transition, fixed-wing cruise and recovery separately. A fixed wing has minimum stable airspeed and turning radius; it may **not hover** without a separate VTOL mode.
- Required usable energy is the integral of phase-dependent power over mission time, plus model uncertainty and reserve for reachable landing/rally sites.
- A 70 mph car for 30 minutes covers 35 road miles; corners may reduce *drone* path length, but cannot be counted without route geometry.
- A two-hour trip is a **separate feasibility case**; require an airframe-specific measured energy-speed curve first.

## Important hard stop
If no safe/authorized flight corridor or safe recovery site exists, **there is no flight plan**. The architecture must accept this as a legitimate and common outcome.

## Initial planning experiments
1. Straight highway: car outruns slower aircraft; range/zoom decays.
2. Mountain switchbacks: shortcut aircraft travels less, but ridge occlusions may invalidate view.
3. Town crossing: airspace, people and moving vehicle overflight make a seemingly easy geometric path ineligible.
4. Rural slow road: prolonged observation within safe corridor.
5. Surprise car right-turn: forecast divergence and coverage loss.
6. Headwind: true airspeed differs from ground speed; reserve collapses.
7. No safe recovery site: reject mission regardless of camera quality.
