# 18 — Driver interaction, operator ergonomics and automation bias (v0.2 DRAFT)

## Two different users
**RPIC / safety operator:** monitors aircraft state and legal/safe flight; needs rich map, alerts, manual override/termination procedure and access to raw telemetry under valid mission conditions.

**Car driver:** should only receive concise **non-directive, optional** hazard observations that can be understood without taking eyes off the road. The car interface must not be a drone flight console.

These roles are separate even if one person owns the system. A person driving cannot be assumed to fulfill flight-crew responsibilities.

## Semantics of an observation
Alerts must distinguish:
- `OBSERVED` — sensor detected something in a known visible location at stated time.
- `INFERRED` — system predicts possible event, with conditions and uncertainty.
- `UNKNOWN` — line-of-sight, freshness, resolution or sensor capacity insufficient.
- `UNAVAILABLE` — aircraft/perception service cannot provide a reliable view.

Do **not** use colors or voice phrasing that imply verified safety, particularly for passing. In an absence of detected vehicles, display **“no reliable aerial confirmation”** rather than **“clear road”**.

## Driver alert rules
- Default to voice-only short advisory alerts for relevant, sufficiently supported hazards; optional passenger-only detail.
- Never advise “pass now,” “overtake in N seconds,” “no oncoming traffic,” or a countdown that could be mistaken for permission to cross lanes.
- Rate-limit repetitive messages, suppress low-value chatter, and allow quiet mode without hiding urgent aircraft-control alarms from the RPIC.
- Prioritize new, material hazards and withdraw stale statements; do not repeat the same threat without changed evidence.
- Announce observation failure only when meaningful; avoid “no alert” being mistaken for assurance.
- If image/map confidence conflicts, provide uncertainty and prohibit strong language.
- Driver interface cannot arm, change flight geofences, suppress airspace constraints, choose emergency landing, or override pilot actions.

## Prototype scripts — examples only
- “Possible stopped vehicle ahead. Last observed twelve seconds ago.”
- “Animal near the road on the next bend. Location uncertain.”
- “Aerial coverage of the upcoming section is unavailable.”

**Never:** “Safe passing zone ahead, you're clear.” This conflates verified road rules and live visibility with a safety guarantee.

## Human factors validation
- Test whether notifications actually improve advance awareness without reducing concentration on traffic directly ahead.
- Evaluate false alarms, missed hazards, overtrust from impressive visual overlays, driver frustration and UI confusion.
- Simulate contradictory drone and windshield observations; driver must default to direct observation and road rules.
- Include varied accents/noisy cabin, multiple speakers, loud music, cellular loss and screen-off operation.
- Test pilot handoff and shift fatigue separately; emergency flight control must not depend on driver interacting while moving.

## OpenAI copilot role
Optional explanatory queries about system status, detected road features and mission logs. Outputs are clearly informational and limited to sanitized current state. An LLM cannot authorize launch, set flight-critical modes, override a failsafe, or decide that passing is safe.

## Operational research gate
No public-road driver alert trial until independent human-factors review, appropriate approval, controlled studies, proven uncertainty communication and explicit safety scope. A polished sci-fi interface does not demonstrate safe behavior.
