# Stallion VTOL Perception

**English** | [Português](README.pt-BR.md)

A 3D-printed fixed-wing VTOL UAV (Flightory Stallion) built as an aerial perception platform. The aircraft streams video and telemetry to a ground station, where an object detection model (YOLO) identifies trained classes such as people and vehicles. Later phases add geolocated detections and multi-sensor fusion.

Target civilian applications: search and rescue, survey of hazardous areas (e.g. humanitarian demining support) and inspection.

<img src="docs/images/airframe-assembled.jpg" alt="Assembled airframe" width="420">

## Status

| Phase | Description | Status |
| --- | --- | --- |
| 0 | Airframe: print, assembly and finishing | Done |
| 1 | Electrical system and flight (manual + VTOL tuning) | In progress |
| 2 | Video and telemetry downlink to the ground station | Planned |
| 3 | Ground-based object detection (pretrained YOLO) | Planned |
| 4 | Custom aerial dataset and fine-tuning | Planned |
| 5 | Geolocation of detections (lat, lon) | Planned |
| 6 | Sensor fusion and tracking | Planned |

Details and completion criteria for each phase: [ROADMAP.md](ROADMAP.md). Build log: [PROGRESS.md](PROGRESS.md).

## Platform

| Item | Value |
| --- | --- |
| Airframe | Flightory Stallion, VTOL version |
| Wingspan / length | 1,340 mm / 990 mm |
| VTOL layout | Tricopter: front tilt motor + fixed rear motors |
| Takeoff weight (AUW, published) | 2,000-3,000 g |
| Cruise speed (published) | 60-70 km/h |
| Material | LW-PLA; ASA for parts that need more strength and heat resistance |

## Components

Based on the Flightory specification. The list will be updated as parts are installed.

| Item | Choice |
| --- | --- |
| Flight controller | SpeedyBee F405 |
| GPS | Matek M10Q |
| Motors | EMAX |
| Camera | Moonlight with GM3 gimbal |
| Firmware | ArduPilot (QuadPlane, tilt-rotor) |

## Architecture

Inference runs on the ground station, not on board, which keeps the aircraft light and allows a larger model.

```mermaid
flowchart LR
  subgraph Aircraft
    FC[Flight controller<br/>ArduPilot, GPS, IMU]
    CAM[Camera<br/>mission video]
    SENS[Extra sensors<br/>planned: thermal, LiDAR]
  end
  subgraph Ground station
    RX[Receiver<br/>video + telemetry]
    DET[YOLO detector<br/>people, vehicles]
    FUS[Sensor fusion<br/>geolocated targets]
    OPS[Operator console<br/>map and alerts]
  end
  FC -- telemetry / MAVLink --> RX
  CAM -- video --> RX
  SENS -. data .-> RX
  RX --> DET --> FUS --> OPS
```

Planned software stack: ArduPilot, Mission Planner / QGroundControl, pymavlink or MAVROS, ROS 2, Python, PyTorch, Ultralytics YOLO, OpenCV.

## Metrics to report

- mAP@0.5 per class on a held-out aerial test set
- Inference FPS on the ground-station hardware
- End-to-end latency (camera to detection on screen)
- Geolocation error in meters against targets with known GPS coordinates
- Detection gain from sensor fusion versus camera only

## Safety

Test flights follow the local UAS regulations, in authorized areas, within visual line of sight and with failsafe (RTL on link loss) configured. Datasets published here will not contain identifiable people.

## Credits

Airframe design by [Flightory](https://flightory.com). Specifications from [SkyRaccoon](https://www.skyraccoon.com/aircraft/Flightory_Stallion-VTOL_stallion) and [DroneXL](https://dronexl.co/2025/04/04/3d-printed-stallion-drone-diy-fixed-wing/).

## Author

Bruno Barroso, M.S. in Artificial Intelligence student at the University of Colorado Boulder. [LinkedIn](https://www.linkedin.com/in/brunobarroso-ai/)
