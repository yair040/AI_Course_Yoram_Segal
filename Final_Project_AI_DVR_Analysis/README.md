Author: Yair Levi# 📹 AI-Powered DVR Security Camera Analysis

> **Multi-Agent Natural Language Search & Automated Email Notification System**  
> for Provision ISR DVR Surveillance Footage
 
> **Author: Yair Levi**

---

## 🧭 Overview

This project builds an end-to-end AI pipeline that lets you search recorded security camera footage using plain language — no manual scrubbing required. You describe what you're looking for, an AI agent locates the matching video segment, and a second AI agent emails you the result automatically.

**Example flow:**
```
You:      "Find the moment someone entered through the back gate on Tuesday evening."
Agent 1:  "Do you mean the main back gate or the parking lot gate?"
You:      "The main back gate."
Agent 1:  [searches footage] → Match found at 21:43:17
Agent 2:  [sends email] → "Event detected at 21:43:17 — see attached thumbnail."
```

---

## 🎯 Research Question

> *What is an effective method for using AI agents to locate relevant segments in surveillance video based on natural language instructions and automatically trigger email notifications when a match is found?*

See [`AI_DVR_PreparatoryReport.docx`](./docs/AI_DVR_PreparatoryReport.docx) for the full research framing, gap analysis, and methodology.

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                          USER INTERFACE                             │
│              (CLI / Web chat — Hebrew & English supported)          │
└──────────────────────────────┬──────────────────────────────────────┘
                               │ natural language query
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        AGENT 1  (Query)                             │
│  • Conversational clarification loop                                │
│  • Formalises query → structured search parameters                  │
│  • Model: GPT-4o / Claude                                           │
└──────────────────────────────┬──────────────────────────────────────┘
                               │ {camera_id, time_range, description}
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     LAYER 1  (DVR Ingestion)                        │
│  • Connects to Provision ISR DVR via RTSP or local NAS              │
│  • Pulls recorded segments with FFmpeg / OpenCV                     │
│  • Splits into overlapping 30-second chunks                         │
└──────────────────────────────┬──────────────────────────────────────┘
                               │ video chunks
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     LAYER 3  (Detection)                            │
│  • Temporal Video Grounding → [t_start, t_end]                      │
│  • VLM zero-shot or fine-tuned VTG model                            │
│  • Conservative-bias calibration step                               │
└──────────────────────────────┬──────────────────────────────────────┘
                               │ detection result + confidence
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        AGENT 2  (Notify)                            │
│  • Composes email: timestamp + thumbnail + description              │
│  • Sends via SMTP / Gmail API / SendGrid                            │
│  • Logs event to local database                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## ✨ What Makes This Project Different

This project addresses **6 gaps** not covered by existing research (OmAgent, VideoAgent2, SPOT!, UNC Charlotte VLM pipeline):

| Gap | Description |
|-----|-------------|
| **G-1** | First integration layer specifically built for **Provision ISR DVR** hardware (RTSP recorded-segment retrieval) |
| **G-2** | **Conversational multi-turn Agent 1** — asks clarifying questions before committing to retrieval |
| **G-3** | **End-to-end latency benchmarking** on consumer DVR hardware |
| **G-4** | **Hebrew / RTL natural-language query support** — absent from all reviewed literature |
| **G-5** | Open reproducible implementation of the USPTO Patent 12,367,677 two-model architecture |
| **G-6** | **Conservative-bias mitigation** for VLM zero-shot detection on real CCTV footage |

Full analysis in [`AI_DVR_PreparatoryReport.docx`](./docs/AI_DVR_PreparatoryReport.docx) — Section 3.

---

## 📁 Project Structure

```
ai-dvr-analysis/
│
├── README.md                        ← You are here
│
├── docs/
│   ├── AI_DVR_PreparatoryReport.docx    ← Full preparatory report
│   └── AI_DVR_LiteratureResources.docx  ← 14 academic references (Q1/Q2, 2023–2025)
│
├── src/
│   ├── ingestion/
│   │   ├── rtsp_puller.py           ← Connect to Provision ISR DVR via RTSP
│   │   ├── segment_chunker.py       ← Split footage into overlapping chunks
│   │   └── nas_reader.py            ← Read from local NAS storage
│   │
│   ├── agents/
│   │   ├── agent1_query.py          ← Conversational query agent (LLM)
│   │   └── agent2_notify.py         ← Email notification agent
│   │
│   ├── detection/
│   │   ├── vtg_model.py             ← Temporal video grounding
│   │   ├── vlm_detector.py          ← VLM zero-shot detection (CLIP-based)
│   │   └── calibrator.py            ← Conservative-bias mitigation
│   │
│   └── pipeline.py                  ← End-to-end pipeline orchestrator
│
├── config/
│   ├── dvr_config.yaml              ← DVR connection settings (RTSP URL, credentials)
│   ├── email_config.yaml            ← SMTP / Gmail API settings
│   └── model_config.yaml            ← Model selection and thresholds
│
├── tests/
│   ├── test_ingestion.py
│   ├── test_agent1.py
│   ├── test_detection.py
│   └── test_agent2.py
│
├── notebooks/
│   └── exploration.ipynb            ← Exploratory analysis and model evaluation
│
├── requirements.txt
└── .env.example                     ← Environment variable template
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10+
- FFmpeg installed and in PATH
- Access to a Provision ISR DVR (RTSP stream URL or NAS path)
- OpenAI or Anthropic API key (for Agent 1 & Agent 2)
- Gmail / SMTP credentials (for email notifications)

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/your-org/ai-dvr-analysis.git
cd ai-dvr-analysis

# 2. Create a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Copy and fill in environment variables
cp .env.example .env
# Edit .env with your DVR credentials, API keys, and email settings
```

### Configuration

Edit `config/dvr_config.yaml` with your Provision ISR details:

```yaml
dvr:
  rtsp_url: "rtsp://<username>:<password>@<dvr-ip>:<port>/cam/realmonitor?channel=1&subtype=0"
  nas_path: null               # Optional: local NAS mount path
  chunk_duration_sec: 30
  chunk_overlap_sec: 5
  cameras:
    - id: 1
      label: "Front entrance"
    - id: 2
      label: "Back gate"
```

### Run

```bash
# Start the interactive pipeline
python src/pipeline.py

# You will be prompted by Agent 1:
# > What would you like to find in the footage?
```

---

## 🧪 Development Phases

| Phase | Title | Status |
|-------|-------|--------|
| 1 | DVR Integration & Ingestion | 🔲 Not started |
| 2 | Agent 1 — Conversational Query | 🔲 Not started |
| 3 | Detection Module | 🔲 Not started |
| 4 | Agent 2 — Email Notification | 🔲 Not started |
| 5 | Integration & Evaluation | 🔲 Not started |

Update the status emoji as work progresses:  
`🔲 Not started` → `🔄 In progress` → `✅ Complete`

---

## 📊 Evaluation Metrics

| # | Metric | Target |
|---|--------|--------|
| M-1 | Temporal localisation accuracy (IoU) | > 0.5 on held-out footage |
| M-2 | Alert precision (true positives / total alerts) | ≥ 58% improvement over motion-detection baseline |
| M-3 | End-to-end latency (query → email) | < 60 seconds on DVR hardware |
| M-4 | Dialogue turns before retrieval | ≤ 2 clarification rounds on average |
| M-5 | Hebrew/English accuracy parity | < 5% accuracy gap |

---

## 📚 Academic Literature

All references are documented in [`docs/AI_DVR_LiteratureResources.docx`](./docs/AI_DVR_LiteratureResources.docx).  
All 14 papers are **Q1 or Q2 ranked**, published **2023–2025**, and **freely accessible**.

| Pillar | Key Paper | Free Link |
|--------|-----------|-----------|
| VAD Survey | Bao et al. 2024 — ACM Comp. Surveys | [arXiv 2405.10347](https://arxiv.org/abs/2405.10347) |
| Real CCTV VAD | Yao et al. 2025 — IEEE conf. | [arXiv 2603.04727](https://arxiv.org/abs/2603.04727) |
| LLM VAD Review | Zanella et al. 2024 — IEEE TPAMI | [arXiv 2412.18298](https://arxiv.org/abs/2412.18298) |
| Temporal Grounding | Zhang et al. 2023 — ICCV | [arXiv 2307.10567](https://arxiv.org/abs/2307.10567) |
| DVR Agent Loop | OmAgent 2024 — NeurIPS | [arXiv 2406.16620](https://arxiv.org/abs/2406.16620) |
| Detect → Notify | USPTO Patent 12,367,677 | [Google Patents](https://patents.google.com/patent/US12367677B2) |
| Full Pipeline | UNC Charlotte VLM 2025 | [arXiv 2508.11690](https://arxiv.org/abs/2508.11690) |

---

## ⚙️ Environment Variables

Copy `.env.example` to `.env` and fill in:

```env
# LLM Provider (choose one)
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...

# DVR Connection
DVR_RTSP_URL=rtsp://admin:password@192.168.1.100:554
DVR_USERNAME=admin
DVR_PASSWORD=your_password

# Email Notification (Agent 2)
EMAIL_SENDER=your@gmail.com
EMAIL_RECIPIENT=alert@yourdomain.com
GMAIL_APP_PASSWORD=xxxx-xxxx-xxxx-xxxx
# Or use SMTP:
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
```

> ⚠️ Never commit your `.env` file. It is listed in `.gitignore`.

---

## 🗺️ Roadmap

- [ ] Phase 1 — Provision ISR RTSP ingestion layer
- [ ] Phase 2 — Agent 1 conversational dialogue (English)
- [ ] Phase 2b — Hebrew / RTL support for Agent 1
- [ ] Phase 3 — VTG detection module + calibration
- [ ] Phase 4 — Agent 2 email notification
- [ ] Phase 5 — End-to-end evaluation & benchmarking
- [ ] Phase 6 — Multi-camera support
- [ ] Phase 7 — Web UI dashboard

---

## 📄 Documents

| Document | Purpose |
|----------|---------|
| [`AI_DVR_PreparatoryReport.docx`](./docs/AI_DVR_PreparatoryReport.docx) | Research question, gap analysis, methodology, architecture |
| [`AI_DVR_LiteratureResources.docx`](./docs/AI_DVR_LiteratureResources.docx) | 14 academic references with DOIs, rankings, and access links |
| [`README.md`](./README.md) | This file — project overview and getting started guide |

---

## 📜 License

MIT License — see `LICENSE` for details.

---

*Project initiated May 2025 · Author: Yair Levi · Academic literature: Q1/Q2 journals, 2023–2025*
