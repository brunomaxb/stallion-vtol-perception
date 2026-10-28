# Roadmap

Each phase ends with a verifiable completion criterion. Tasks are checked off as they are done; dated notes go to [PROGRESS.md](PROGRESS.md).

## Phase 0 - Airframe (done)

- [x] Print parts (LW-PLA; ASA for high-load and heat-exposed parts)
- [x] Assemble fuselage, wings and tail
- [x] Finishing and painting

## Phase 1 - Electrical system and flight

- [ ] Final component list (ESCs, radio, battery, exact EMAX motor model)
- [ ] Install motors, ESCs and servos
- [ ] Install SpeedyBee F405, Matek M10Q GPS and radio receiver
- [ ] Flash ArduPilot and load base parameters (QuadPlane, tilt-rotor)
- [ ] Bench tests: motor direction, servo throws, failsafe
- [ ] Hover tests in VTOL mode
- [ ] Fixed-wing flight and VTOL transition tuning

**Done when:** stable transitions between VTOL and fixed-wing flight.

## Phase 2 - Downlink to the ground station

- [ ] Video downlink to the ground station
- [ ] Telemetry (MAVLink) on the ground station
- [ ] Video frames available to Python/OpenCV on the ground computer
- [ ] RTL failsafe on link loss tested in flight

**Done when:** live video and telemetry on the ground station.

## Phase 3 - Ground-based detection

- [ ] Pretrained YOLO (COCO) running on recorded and live video
- [ ] Classes: person, vehicle
- [ ] Measure inference FPS and end-to-end latency

**Done when:** FPS and latency measured and reported.

Can start in parallel with phase 1 using public aerial footage.

## Phase 4 - Custom dataset

- [ ] Collect and label own aerial images (no identifiable people in the published set)
- [ ] Fine-tune YOLO
- [ ] Evaluate on a held-out test set

**Done when:** mAP above the pretrained baseline on the test set.

## Phase 5 - Geolocation

- [ ] Camera pose from telemetry (GPS, attitude, gimbal angle)
- [ ] Project detections to map coordinates (lat, lon)
- [ ] Validate against targets with known coordinates

**Done when:** geolocation error measured in meters.

## Phase 6 - Sensor fusion and tracking

- [ ] Add a second sensor (thermal or LiDAR/rangefinder)
- [ ] Fuse with camera detections
- [ ] Multi-object tracking

**Done when:** measured gain over camera only.
