# IRON DEFENSE

### AI-Powered Student Scam, Phishing & Social-Engineering Detection System

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen.svg)](#)
[![Test Suite](https://img.shields.io/badge/pytest-18%2F18%20passed-success.svg)](#)
[![Python Version](https://img.shields.io/badge/python-3.11%2B-blue.svg)](#)
[![Node Version](https://img.shields.io/badge/node-%3E%3D20.x-blue.svg)](#)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](#)

---

## 1. Context & Overview

### Elevator Pitch & Value Proposition
University students are primary targets for targeted spearphishing, credential harvesting, fee-payment fraud, internship scams, and quishing (QR-code phishing). **Iron Defense** is a high-throughput, explainable cybersecurity engine designed to detect, analyze, and mitigate digital threats targeting academic communities.

### Core Features
* **Multi-Source Evidence Fusion**: Deduplicates and normalizes signals across NLP models, social engineering rules, URL feature extraction, threat intelligence, OCR, and QR decoding.
* **Dual Local NLP Engine**:
  * **Primary**: Fine-tuned **DeBERTa** (`deberta-scam-final`) sequence classification model.
  * **Backup**: Local **Ollama** (`qwen3.5:9b`) structured JSON semantic analyzer.
* **Explainable Risk Engine**: Calculates a transparent 0–100 evidence-fusion risk score with complete signal breakdowns (`score_breakdown`) without fabricating uncalibrated ML probabilities.
* **Behavioral MITRE ATT&CK Mapping**: Maps detected TTPs (e.g., `T1566.002` Spearphishing Link, `T1566.001` Spearphishing Attachment) with clear attribution disclaimers.
* **Automated Attack Story & Safe Actions**: Generates human-readable incident narratives and prioritized victim mitigation steps.

---

## 2. Architecture & System Design

### Architecture Diagram

```
┌────────────────────────────────────────────────────────────────────────┐
│                          USER INTERFACE LAYER                          │
│               React 19 + TypeScript + Vite + Tailwind CSS               │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ HTTP REST API
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                        FASTAPI BACKEND SERVICES                        │
│                                                                        │
│  ┌───────────────────────┐ ┌───────────────────┐ ┌──────────────────┐  │
│  │   Input Preprocessing  │ │ Unified Analyzer  │ │ Database ORM     │  │
│  │ (URL/OCR/QR Extractors)│ │  (Services Layer) │ │(SQLite/SQLAlchemy│  │
│  └───────────┬───────────┘ └─────────┬─────────┘ └────────┬─────────┘  │
└──────────────┼───────────────────────┼────────────────────┼────────────┘
               │                       │                    │
               ▼                       ▼                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                          INDIVIDUAL ANALYZERS                          │
│  ┌────────────────────┐ ┌───────────────────┐ ┌─────────────────────┐  │
│  │   DeBERTa Model    │ │  Ollama qwen3.5   │ │ Social Engineering  │  │
│  │(Sequence Classif.) │ │(Semantic Structured│ │   Regex Engine      │  │
│  └────────────────────┘ └───────────────────┘ └─────────────────────┘  │
│  ┌────────────────────┐ ┌───────────────────┐ ┌─────────────────────┐  │
│  │    URL Analyzer    │ │   Threat Intel    │ │  OpenCV / PyZbar &  │  │
│  │(15+ Risk Features) │ │(VirusTotal/Demo)  │ │   Pytesseract OCR   │  │
│  └────────────────────┘ └───────────────────┘ └─────────────────────┘  │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                        EVIDENCE FUSION & RISK ENGINE                   │
│                                                                        │
│   ┌─────────────────────┐ ┌────────────────────┐ ┌──────────────────┐  │
│   │ Signal Deduplication│ │ 0-100 Risk Engine  │ │  MITRE ATT&CK    │  │
│   │     & Normalizer    │ │ Score & Breakdown  │ │ Behavioral Map   │  │
│   └─────────────────────┘ └────────────────────┘ └──────────────────┘  │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                     ATTACK STORY & SAFE ACTIONS                        │
└────────────────────────────────────────────────────────────────────────┘
```

### End-to-End Data Execution Flow
`User Input (Text / URL / Image)` ➔ `Preprocessing (OCR / QR)` ➔ `Parallel Analyzers` ➔ `Evidence Fusion` ➔ `Risk Engine` ➔ `MITRE & Story Mapping` ➔ `JSON Response`.

### Documentation Links
* **Interactive OpenAPI/Swagger Documentation**: `http://localhost:8000/docs`
* **Service Health Endpoint**: `http://localhost:8000/api/health`

---

## 3. Installation & Configuration

### Tech Stack & Prerequisites
* **Python**: `>= 3.11`
* **Node.js**: `>= 20.x`
* **Frameworks**: FastAPI, Pydantic v2, Uvicorn, React 19, Vite, Tailwind CSS
* **ML Stack**: PyTorch (`torch`), HuggingFace `transformers`, Ollama (`qwen3.5:9b`)

### Step-by-Step Local Setup

1. **Clone Repository & Backend Setup**:
```bash
git clone https://github.com/IronDefense/Iron-Defense.git
cd Iron-Defense/backend

# Create virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install backend dependencies
pip install -r requirements.txt
```

2. **Frontend Setup**:
```bash
cd ../frontend
npm install
npm run build
```

3. **Running the Application**:
```bash
# Terminal 1 - Backend (Port 8000)
cd backend
python -m uvicorn app.main:app --host 0.0.0.0 --port 8000

# Terminal 2 - Frontend Hosted Page (Port 5173)
cd frontend
npm run preview -- --host 0.0.0.0 --port 5173
```

### Environment Variables Matrix (`.env`)

| Variable Name | Description | Type | Default | Required |
| :--- | :--- | :--- | :--- | :--- |
| `APP_NAME` | Service application name | String | `Iron Defense` | Yes |
| `APP_ENV` | Environment stage (`development`/`production`) | String | `development` | Yes |
| `DATABASE_URL` | SQLite database URI | String | `sqlite:///./iron_defense.db` | Yes |
| `LLM_PROVIDER` | Local LLM provider (`ollama`/`local`) | String | `ollama` | Yes |
| `OLLAMA_BASE_URL` | Local Ollama API host | URL | `http://localhost:11434` | Yes |
| `OLLAMA_MODEL` | Ollama model identifier | String | `qwen3.5:9b` | Yes |
| `DEBERTA_MODEL_PATH` | Path to fine-tuned DeBERTa model directory | Path | `C:/Iron-Defense/hugg/models/deberta-scam-final` | Optional |
| `THREAT_INTEL_PROVIDER` | Threat intel provider (`mock`/`virustotal`) | String | `mock` | Yes |
| `MAX_UPLOAD_SIZE_MB` | Maximum allowed image file size | Integer | `10` | Yes |

---

## 4. Developer Experience & Quality Control

### Usage Snippet (API Request)

```bash
curl -X POST "http://localhost:8000/api/analyze" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "URGENT! Your university account will be suspended today. Verify your password immediately at https://student-account-verification.example/login",
    "url": "https://student-account-verification.example/login",
    "source": "sms"
  }'
```

### Testing & QA Commands

```bash
cd backend
python -m pytest -v
```

Tests cover health checks, social engineering regex rules, URL feature risk calculations, evidence fusion bounds, and end-to-end endpoint execution.

---

## 5. Reliability, Performance & Security

### Benchmarks & Maturity Status
* **Status**: Production-Ready (Demo Tested).
* **Mock/Local Mode Latency**: `< 150ms` per message analysis.
* **Local DeBERTa Inference**: `~200ms` on CPU.

### Troubleshooting & Known Limitations
* **Missing Tesseract Binary**: If Tesseract OCR binary is not installed, the OCR engine gracefully logs a warning and returns empty text without crashing.
* **PyTorch CPU FP16 NaNs**: DeBERTa models loaded from float16 weights are automatically converted to `float32` precision (`.to(torch.float32)`) for CPU inference.

### Security Reporting
Treat all incoming user inputs as untrusted. Iron Defense performs strictly passive analysis and **never executes uploaded files or fetches arbitrary remote URLs**. To report security vulnerabilities, contact `security@irondefense.org`.

---

## 6. Governance & License

Distributed under the **MIT License**. Free for educational, academic, and open-source hackathon research.
