# 🚦 Smart City — Vehicle Speed Detection System

> Automatically detect vehicles, estimate speed in real-time, and flag violators with red bounding boxes — from any road video. No hardware. No training. No dataset needed.

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square&logo=python)
![YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-purple?style=flat-square)
![OpenCV](https://img.shields.io/badge/OpenCV-4.8+-green?style=flat-square&logo=opencv)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Kaggle%20%7C%20Local%20%7C%20Web-orange?style=flat-square)

---

## 🎬 Output Preview



> Upload a road video → system processes it → download the annotated output

| Vehicle within limit | Vehicle speeding |
|---|---|
| 🟢 Green bounding box | 🔴 Red bounding box + ⚠ OVER LIMIT label |

**The output video shows:**
- Each vehicle tracked with a unique ID
- Speed displayed live on each bounding box (km/h)
- Green box = safe, Red box = violation
- Top-left HUD with live stats (vehicles, violations, FPS, scale)

---

## ✅ What This System Does

- 📹 Upload any road/traffic video (MP4, AVI, dashcam, CCTV)
- 🚗 Detects vehicles: **cars, bikes, buses, trucks** (YOLOv8 pre-trained)
- 📐 **Auto-calibrates** pixel-to-meter scale — no manual input needed
- 🔢 Tracks each vehicle with a **unique ID** across all frames (DeepSORT)
- ⚡ Estimates **real-time speed** (km/h) per vehicle
- 🟢 **Green box** = within speed limit
- 🔴 **Red box** = speed violation with warning
- 📊 Live HUD overlay on the video
- ⬇️ Output video **auto-downloaded** after processing
- ❌ **No training, no dataset, no fine-tuning needed**

---

## 🧠 How Speed is Calculated (No Hardware Needed)

### Step 1 — Auto Pixel Scale
YOLOv8 detects cars and measures their bounding box in pixels.
Since the average car is **4.5 meters** long:

```
pixels_per_meter = bounding_box_width_px ÷ 4.5
```

The system collects **30 samples** and takes the **median** to reject outliers. Scale locks automatically.

### Step 2 — Speed from Pixel Movement
DeepSORT tracks each vehicle's center `(cx, cy)` across frames:

```
pixel_distance = √((cx2−cx1)² + (cy2−cy1)²)
meter_distance = pixel_distance ÷ pixels_per_meter
speed_kmh      = (meter_distance ÷ elapsed_seconds) × 3.6
```

### Step 3 — Smoothing (EMA)
```
smoothed_speed = 0.6 × previous_speed + 0.4 × new_speed
```

---

## 🛠️ Tech Stack

| Component | Tool | Purpose |
|---|---|---|
| Vehicle Detection | YOLOv8 (ultralytics) | Detect vehicles in each frame |
| Object Tracking | DeepSORT | Unique ID per vehicle across frames |
| Speed Estimation | OpenCV + Math | Pixel displacement → km/h |
| Auto Calibration | Custom Algorithm | Estimate px/meter from vehicle size |
| Video Processing | OpenCV (cv2) | Read frames, draw boxes, write output |
| Video Re-encoding | FFmpeg | Convert to H.264 for browser playback |
| Backend (Web) | FastAPI | REST API for video upload (coming soon) |
| Frontend (Web) | HTML/CSS/JS | Upload UI + preview (coming soon) |

---

## 📁 Project Structure

```
smart-city-speed-detection/
├── speed_detection_kaggle.py   ← Main Kaggle notebook script
├── cell_8_9_fixed.py           ← Fixed save + auto-download cells
├── backend/
│   ├── main.py                 ← FastAPI backend (coming soon)
│   ├── detector.py             ← YOLOv8 + DeepSORT pipeline
│   └── requirements.txt
├── frontend/
│   ├── index.html              ← Web UI (coming soon)
│   └── style.css
└── README.md
```

---

## 📦 Requirements

```
ultralytics>=8.0.0
deep-sort-realtime>=1.3.2
opencv-python-headless>=4.8.0
numpy>=1.24.0
fastapi>=0.104.0
uvicorn>=0.24.0
```

Install all:
```bash
pip install ultralytics deep-sort-realtime opencv-python-headless numpy fastapi uvicorn
```

---

## 🎯 Tips for Best Results

- Use **dashcam or CCTV footage** where cars move clearly across the frame
- Videos shot **from the side** (perpendicular to traffic) give most accurate speed
- Avoid videos shot **from behind** — scale estimation is harder
- **30 FPS or higher** gives smoother speed readings
- Good lighting and clear visibility improves YOLOv8 detection accuracy

---
