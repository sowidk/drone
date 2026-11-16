# 05 — Perception, traffic intelligence and driver alerts (v0.1 DRAFT)

## Sensor roles
- Stabilized wide-FOV electro-optical camera: scene awareness and near/medium range object detection.
- Stabilized optical-zoom camera: targeted distant inspection. High zoom *reduces* field of view; tracker and pointing system must handle this.
- Optional thermal: complementary low-light detection, not a guaranteed animal/person classifier.
- Aircraft GNSS/IMU, gimbal encoders, camera calibration, synchronized timebase: required for geo-referenced tracking.
- Road/map data: context only; may be stale or incomplete.

## Perception pipeline
1. Ingest frames with precise exposure timestamp and camera calibration, aircraft pose, gimbal orientation and uncertainty.
2. Detect classes: motor vehicles, trucks, motorcycles, cyclists, pedestrians, animals, objects, unexpected stopping.
3. Associate detections across frames into tracks with confidence and missing/occluded status.
4. Estimate object motion in road coordinates *only when calibration/parallax/range evidence supports it*; distant monocular imagery cannot reliably provide exact metric speed alone.
5. Fuse multiple sensor observations and road data without treating agreement of two correlated models as independent confirmation.
6. Publish tracks with covariance/error bounds, last-seen time and explicit unknowns.
7. Forecast possible catch-up times and hazard relevance for different driver-route hypotheses.
8. Alert only on independently checked policy, freshness and relevance; suppress stale or spurious chatter.

## Optical reality check
A 4K camera can record sharp-looking footage yet be unable to resolve a pedestrian a mile away with a wide lens. Target pixels depend on focal length/FOV, range and orientation; optical zoom can recover detail at the cost of surveillance footprint and stabilization demands. Determine **minimum task-specific pixel density empirically** using validated labeled images, not marketing “4K” or advertised transmission range.

## Traffic prediction
- Relative speed plus observed distance can estimate catch-up **interval**, not a guaranteed moment.
- Account for acceleration, braking, turn intentions, road curvature, lane positions, intersections and temporary occlusion.
- Do not invent unseen oncoming traffic: unknown segments remain unknown.
- A slower car being observed does not make a future overtake legal or safe.
- Passing-zone layer can report **verified markings and restrictions** for advance route awareness, never a real-time permissive signal, steering suggestion, or clear-to-pass countdown.

## Driver interaction
Examples of acceptable notifications:
- “Observed slower traffic ahead; estimate is uncertain.”
- “Possible wildlife near the next bend; last seen 4 seconds ago.”
- “Aerial view of the next road section is unavailable.”

Not acceptable:
- “Road clear — pass now.”
- “You have 7 seconds to pass.”
- Any alert omitting material uncertainty or implying a guarantee of no oncoming vehicles.

The interface is voice-first for short alerts, with limits on repetition and urgency. It must not demand a driver's visual attention.

## Evaluation
- Per class: precision, recall, false-alert rates, miss rates and confidence calibration, stratified by altitude, range, weather, lighting, occlusion and camera movement.
- Tracking: identity switches, position/velocity error and uncertainty coverage.
- Forecasts: interval coverage, calibration, false-certainty rate and staleness.
- End-to-end: meaningful warning lead time versus alert latency, information overload and detection gaps.
- Safety property: **no observation** and **no detection** are different states, and neither is proof of a clear road.

Initial experiments should use controlled synthetic traffic scenes and properly licensed/consented public datasets, not live unsupervised flights.
