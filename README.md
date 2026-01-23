# 🚗 Smart Traffic Monitoring System
A real-time AI-based system for traffic flow analysis, speed estimation, and congestion warning.

![Python](https://img.shields.io/badge/Python-3.12-blue)
![YOLOv8](https://img.shields.io/badge/YOLO-v8-green)
![OpenCV](https://img.shields.io/badge/OpenCV-Computer%20Vision-red)
![Tracker](https://img.shields.io/badge/Tracker-ByteTrack-orange)

## 📖 Overview

Smart Traffic Monitoring System is a computer vision project designed to automate traffic surveillance. By leveraging YOLOv8 (fine-tuned) and ByteTrack, the system detects vehicles, estimates their speeds, and analyzes traffic density to provide real-time congestion warnings.

The system is optimized for performance using multi-threading and asynchronous processing, capable of running at 30 FPS on standard hardware.

(Replace this image with a screenshot of your actual dashboard)

## ✨ Key Features

* **Multi-class vehicle detection:** Fine-tuned YOLOv8 model specifically for 4 classes: `bus`, `car`, `motor`, `truck`.
* **Robust tracking:** Implements **ByteTrack** (via `my_tracker.yaml`) to maintain consistent Object IDs across frames, handling occlusion better than standard methods.
* **Speed estimation:** Calculates vehicle speed (km/h) using perspective transform (Homography) to map 2D pixels to real-world coordinates.
* **Advanced traffic analytics:** 
  * Calculates occupancy rate (Density) based on ROI pixel area. 
  * Classifies traffic status into 5 levels: Free Flow, Moderate, High Density, Slow Traffic, Congestion.
* **Real-time dashboard:** visualizes traffic flow trends using Matplotlib (Agg backend for high performance) integrated directly into the video feed.
* **Asynchronous I/O:** Uses a separate thread for RTSP video capturing (TCP transport) to ensure low latency and prevent bottlenecking.

## 🏆 Performance
The system has been benchmarked on AMD Ryzen 5 6600H  / NVIDIA GeForce RTX 3060ti (4GB VRAM).
| Metric | Value | Note |
| :--- | :--- | :--- |
| mAP@0.5 | 95.3% | Fine-tuned model (traffic4.pt) |
| Processing speed | 25 - 30 FPS | Real-time performance |
| Inference time | ~12 ms| Using YOLOv8 nano |
| Total latency | ~50 ms| Including logic and rendering |

<small>*Training metrics showing convergence and high mAP.*</small>

## ⚙️ How It Works (Logic Overview)

1. **Input processing:** Captures video frames from RTSP streams (simulated via MediaMTX/FFmpeg) or local files using a dedicated thread and queue to avoid bottlenecks.
2. **Detection:** YOLOv8 predicts bounding boxes, classes, and confidence scores for vehicles.
3. **Tracking:** ByteTrack assigns unique IDs to detected objects.
4. Counting Logic:
- A Polygon ROI (Region of Interest) is drawn on the frame.
- The system checks if the center point (anchor) of a vehicle's box is inside the Polygon.
- If inside + New ID -> Increment Counter (vehicle_counts).
5. **Speed Estimation:** Applies perspective transform to convert pixel displacements to real-world speeds (km/h).
6. **Traffic Analytics:** Calculates occupancy rate within ROI, computes average speed, counts vehicles, and classifies traffic state (free flow, slow, congestion).
7. **Output:** Overlays detections, tracks, speeds, stats, and charts on the video; displays via a GUI dashboard.

## 📸 Demo & Visualization

### 1. Object Detection (Snapshot)

![Detection Sample](assets/image01.jpg)
<p align="center">
  <em>Here is an example of the model detecting vehicles with bounding boxes and class labels..</em>
</p>

### 2. Traffic analytics & dashboard (Video Demos)
The dashboard displays real-time analytics including occupancy rate, average speed, vehicle counts by class, and congestion warnings (e.g., "Congestion" in red). Charts are rendered using Matplotlib and overlaid on the video feed.

![video-demo](assets/video-demo.gif)
<p align="center">
  <em>Example of the GUI dashboard showing traffic density, speed stats, and congestion classification on a test video.</em>
</p>

## 🛠️ Project Structure

```text
smart-traffic-monitoring/
│
├── assets/                  # Demo media (images/videos) and tracker config
│   ├── my_tracker.yaml      # ByteTrack configuration file
│   └── traffic4.pt          # Trained YOLOv8 model weights
├── configs/                 # Dataset config (data.yaml)
├── src/                     # Source code
│   ├── data_preprocessing/  # Scripts for dataset preparation
│   ├── train.py             # Training script
│   └── utils.py             # Helper functions (frame extraction, etc.)
├── main.py                  # Main inference & counting script
├── requirements.txt         # Dependencies (ultralytics, opencv, etc.)
└── README.md                # Project documentation
```

## 🚀 Installation & Usage

### 1. Setup Environment
```bash
git clone [https://github.com/NguyenAn080105/smart-traffic-monitoring-system.git](https://github.com/NguyenAn080105/smart-traffic-monitoring-system.git)
cd smart-traffic-monitoring-system
pip install -r requirements.txt
```

### 2. Run Inference (Counting)
Update paths in main.py for your video source (e.g., RTSP URL or local file) and model (traffic4.pt).
```bash
python main.py
```
<small>*Note: Defaults to a sample video like assets/vehicle_vid_13.mp4. For RTSP, ensure MediaMTX or a similar server is running for streaming.*</small>

## 👤 Team
This project was developed by group from class CS311.Q11.
* ** Nguyen Hoang An** - Dataset research, RTSP streaming, data collection/merging, logic for counting, report writing, flowcharts.
* ** Nguyen Pham Thien An ** - Dataset research, model training/fine-tuning, traffic analysis implementation, evaluation.
* ** Phan Tien Dat ** - Tracking/speed estimation algorithms, frame extraction/ROI, dashboard adjustments, report compilation.
