# 🅿️ ParkGuard AI

> **DVR-Based Parking Occupancy Detection & Dual-Channel Alert System**  
> *(formerly: AI-Powered DVR Security Camera Analysis)*

**Author:** Yair Levi  
**Moderator:** Segal Yoram, PhD

---

## 🧭 Overview

**ParkGuard AI** is an end-to-end AI pipeline that downloads recorded footage from a **Provision ISR DVR**, detects vehicles in a user-defined parking zone using **Google MediaPipe**, and dispatches an alert via **Gmail API email and/or Telegram Bot** when a car occupies that zone for 5+ continuous minutes.

The detection zone is defined through a **conversational dialogue with a local LLM (Ollama + Llama 3.2)** running on WSL — no cloud API, no GUI tools, supports Hebrew.

```
User:     [configures camera=3, start=2025-05-20 08:00, end=2025-05-20 10:00]
System:   [downloads segment from Provision ISR DVR → local MP4]
System:   [shows first frame]
LLM:      "Where in this frame is the parking spot you want to monitor?"
User:     "Top-right corner, near the gate pillar"
System:   [draws rectangle] → "Is this correct?"
User:     "Move it slightly left" → [updated] → "Confirmed."
          [processes all frames with MediaPipe vehicle detection]
          [car in zone at 08:43 — timer starts]
          [car still there at 08:48 — 5 minutes reached]
Agent 2:  📧 Email → "Car detected in zone since 08:43. See attached frame."
          🚗 Telegram → "Parking Alert — Camera 3, 08:48"
```

---

## 🏷️ Project Name Change

| Old Name | New Name |
|----------|----------|
| AI-Powered DVR Security Camera Analysis | **ParkGuard AI** |

The new name reflects the specific domain (parking zone enforcement), the AI-driven detection approach, and the guard/alert function. The full technical subtitle is retained in formal documents.

---

## 🎯 Research Question

> *What is an effective method for using AI agents to locate relevant segments in surveillance video based on natural language instructions and automatically trigger notifications — via email or Telegram — when a match is found?*

Full research framing: [`ParkGuardAI_PreparatoryReport.docx`](./docs/ParkGuardAI_PreparatoryReport.docx)

---

## ⚙️ Functional Requirements

| # | Requirement | Technology |
|---|-------------|------------|
| FR-1 | Download DVR segment by camera + time range | FFmpeg over RTSP/ONVIF |
| FR-2 | Frame-by-frame vehicle detection | Google MediaPipe Object Detection |
| FR-3 | Conversational zone selection (Hebrew/English) | Ollama + Llama 3.2 1B on WSL |
| FR-4 | Detect car in zone for ≥5 continuous minutes | Python state machine + IoU |
| FR-5 | No duplicate alerts while same car is present | Car continuity tracker |
| FR-6 | Gatekeeper: min 10 minutes between alerts | Timestamp gatekeeper |
| FR-7 | Statistics per run | pandas / CSV logger |

---

## 🏗️ System Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│  USER INPUT: camera_id, start_datetime, end_datetime, output_path    │
└─────────────────────────────┬────────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────────┐
│  L0 — DVR INGESTION                                                  │
│  FFmpeg pulls recorded segment from Provision ISR via RTSP/ONVIF     │
└─────────────────────────────┬────────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────────┐
│  L1 — FRAME PROCESSING                                               │
│  OpenCV + MediaPipe Object Detection → vehicle bounding boxes        │
└─────────────────────────────┬────────────────────────────────────────┘
                              ▼ (first frame only)
┌──────────────────────────────────────────────────────────────────────┐
│  L2 — ZONE SELECTION  (Agent 1 — Local LLM)                          │
│  Ollama + Llama 3.2 → ask → draw rectangle → iterate until confirmed │
└─────────────────────────────┬────────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────────┐
│  L3 — OCCUPANCY STATE MACHINE                                        │
│  IoU check → dwell ≥5 min → same car? → gatekeeper (10 min)         │
└─────────────────────────────┬────────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────────┐
│  L4 — DUAL-CHANNEL NOTIFICATION  (Agent 2)                           │
│  📧 Gmail API  +  🚗 Telegram Bot API  (independently configurable)  │
└──────────────────────────────────────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────────┐
│  L5 — STATISTICS                                                     │
│  JSON/CSV: detections, dwell times, alerts per channel, gatekeeper  │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 📦 Related Open-Source GitHub Projects

ParkGuard AI builds on design patterns and algorithms from these six projects:

| Ref | Repository | Stars | Stack | What We Adapt |
|-----|-----------|-------|-------|---------------|
| [GH1] | [SoftServeInc/smartparking](https://github.com/SoftServeInc/smartparking) | ★30 | Python, Docker, MQTT, CNN | Multi-service pipeline architecture; frame windowing strategy |
| [GH2] | [DeepParking/DeepParking](https://github.com/DeepParking/DeepParking) | ★~50 | Python, Redis, REST API | Capture cadence (1 image/15–20s) to reduce CPU load |
| [GH3] | [bhupender0415/CarParkingDetection](https://github.com/bhupender0415/CarParkingDetection) | ★~15 | Python, OpenCV | Preprocessing pipeline; red/green rectangle overlay |
| [GH4] | [mbdelaresma/automated-parking-management-computer-vision](https://github.com/mbdelaresma/automated-parking-management-computer-vision) | ★~20 | Python, YOLOv4, OpenCV | **Timer + unique car ID pattern** (closest existing system) |
| [GH5] | [yashchinchole/Car-Parking-Space-Counter](https://github.com/yashchinchole/Car-Parking-Space-Counter) | ★~30 | Python, OpenCV, CVZone, Haar | Lightweight Haar baseline; pixel-count threshold fallback |
| [GH6] | [benarnav/parking-monitor](https://github.com/benarnav/parking-monitor) | ★~15 | Python, OpenCV | Dwell-time tracking per region; color-based car identity |

**What ParkGuard AI adds beyond all six:** Provision ISR DVR integration, conversational LLM zone selection, Hebrew support, email + Telegram dual-channel alerts, 10-minute gatekeeper, open reproducible implementation.

---

## 📁 Project Structure

```
parkguard-ai/
│
├── README.md
├── docs/
│   └── ParkGuardAI_PreparatoryReport.docx
│
├── src/
│   ├── ingestion/
│   │   ├── dvr_downloader.py       ← Provision ISR RTSP/ONVIF download
│   │   └── frame_extractor.py      ← OpenCV frame pipeline
│   ├── detection/
│   │   ├── mediapipe_detector.py   ← MediaPipe Object Detection wrapper
│   │   └── iou_utils.py            ← IoU + zone overlap logic
│   ├── agents/
│   │   ├── agent1_zone_selector.py ← Ollama LLM zone dialogue
│   │   └── agent2_notifier.py      ← Email + Telegram dispatcher
│   ├── occupancy/
│   │   ├── state_machine.py        ← Dwell timer + gatekeeper
│   │   └── car_tracker.py          ← Same-car continuity tracker
│   ├── statistics/
│   │   └── stats_logger.py         ← Event log + metrics
│   └── pipeline.py                 ← Orchestrator
│
├── config/
│   ├── dvr_config.yaml
│   ├── detection_config.yaml
│   ├── email_config.yaml
│   └── llm_config.yaml
│
├── tests/
├── notebooks/
│   └── exploration.ipynb
├── requirements.txt
└── .env.example
```

---

## 🚀 Getting Started

### Prerequisites

| Requirement | Notes |
|-------------|-------|
| Python 3.10+ | |
| FFmpeg | In PATH |
| WSL 2 (Ubuntu 22.04) | For Ollama |
| Ollama | `ollama pull llama3.2:1b` |
| Provision ISR DVR | RTSP/ONVIF access |
| Google account | Gmail API OAuth2 |
| Telegram Bot | Create via @BotFather |

### Install

```bash
git clone https://github.com/your-org/parkguard-ai.git
cd parkguard-ai
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env   # fill in credentials
wsl -- ollama pull llama3.2:1b
```

### Run

```bash
python src/pipeline.py \
  --camera 3 \
  --start "2025-05-20 08:00:00" \
  --end   "2025-05-20 10:00:00" \
  --output ./downloads/
```

---

## 🔔 Notification Configuration

```env
# Email
EMAIL_ENABLED=true
EMAIL_SENDER=yourproject@gmail.com
EMAIL_RECIPIENT=alert@yourdomain.com

# Telegram
TELEGRAM_ENABLED=true
TELEGRAM_BOT_TOKEN=your_bot_token_from_botfather
TELEGRAM_CHAT_ID=your_chat_or_group_id
```

Both channels can be enabled simultaneously. Each sends a frame thumbnail. The 10-minute gatekeeper applies across both channels combined.

---

## 🧪 Development Phases

| Phase | Title | Status |
|-------|-------|--------|
| 1 | DVR Ingestion (FFmpeg + RTSP) | 🔲 Not started |
| 2 | MediaPipe Detection Module | 🔲 Not started |
| 3 | Agent 1 — LLM Zone Selection | 🔲 Not started |
| 3b | Hebrew RTL Support | 🔲 Not started |
| 4 | Occupancy State Machine | 🔲 Not started |
| 5 | Agent 2 — Gmail Notification | 🔲 Not started |
| 5b | Agent 2 — Telegram Notification | 🔲 Not started |
| 6 | Statistics Module | 🔲 Not started |
| 7 | Integration & Evaluation | 🔲 Not started |

---

## 📊 Evaluation Metrics

| # | Metric | Target |
|---|--------|--------|
| M-1 | Detection precision | > 0.85 |
| M-2 | Occupancy event recall | > 0.90 |
| M-3 | Alert precision | > 0.95 |
| M-4 | Dwell-time accuracy | ±30s on > 90% of events |
| M-5 | End-to-end latency (1-hr segment) | < 5 min |
| M-6 | Zone selection dialogue turns | ≤ 2 on average |
| M-7 | Hebrew vs English accuracy | < 5% IoU gap |
| M-8 | Telegram delivery latency | < 3 seconds |

---

## 📚 Key References

Full 19 academic + 6 GitHub references in [`ParkGuardAI_PreparatoryReport.docx`](./docs/ParkGuardAI_PreparatoryReport.docx).

| # | Source | Link |
|---|--------|------|
| [3] | Bao et al. 2024 — VAD Survey (ACM Q1) | [arXiv 2405.10347](https://arxiv.org/abs/2405.10347) |
| [2] | Yao et al. 2025 — LLMs on Real CCTV | [arXiv 2603.04727](https://arxiv.org/abs/2603.04727) |
| [8] | OmAgent 2024 — CCTV Agent Loop | [arXiv 2406.16620](https://arxiv.org/abs/2406.16620) |
| [13] | USPTO Patent 12,367,677 | [Google Patents](https://patents.google.com/patent/US12367677B2) |
| [16] | Google MediaPipe Object Detection | [ai.google.dev](https://ai.google.dev/edge/mediapipe/solutions/vision/object_detector) |
| [19] | Telegram Bot API | [core.telegram.org](https://core.telegram.org/bots/api) |
| [GH4] | Park EZ — closest GitHub system | [github.com/mbdelaresma/...](https://github.com/mbdelaresma/automated-parking-management-computer-vision) |

---

## 📜 License

MIT License — see `LICENSE` for details.

---

*ParkGuard AI · May 2025 · Author: Yair Levi · Moderator: Segal Yoram*  
*Formerly: AI-Powered DVR Security Camera Analysis*
