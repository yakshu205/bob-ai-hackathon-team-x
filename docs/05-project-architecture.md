# Project Architecture — Mission Readiness & Predictive Maintenance Copilot

## High-Level Architecture
```
┌───────────────────────────┐      ┌───────────────────────────┐      ┌───────────────────────────┐
│     Frontend (React 19)   │ ───▶ │     Backend (FastAPI)     │ ───▶ │     ML Model Registry     │
│   Dashboard, Gauges, Chat │ ◀─── │  REST Endpoints + WS Hub  │ ◀─── │ IMS Bearing, AI4I, N-CMAP │
└───────────────────────────┘      └─────────────┬─────────────┘      └───────────────────────────┘
                                                 │
                                                 ▼
                                   ┌───────────────────────────┐
                                   │  Copilot Reasoner & XAI   │
                                   │ Groq Llama 3.3 70B / RAG  │
                                   └─────────────┬─────────────┘
                                                 │
                                                 ▼
                                   ┌───────────────────────────┐
                                   │  SQLite Telemetry Store   │
                                   │   assets, batches, orders │
                                   └───────────────────────────┘
```

## Repository & Source Code Layout
```
bob-ai-hackathon-team-x/
│
├── src/
│   ├── backend/                              # FastAPI Python Backend
│   │   ├── api/                              # REST and WebSocket route handlers
│   │   │   ├── routes_assets.py              # /api/assets, /api/metrics, /api/work-orders
│   │   │   ├── routes_batch.py               # /api/upload-csv, /api/batch-runs
│   │   │   ├── routes_chat.py                # /api/chat, /api/chat/status, Copilot LLM
│   │   │   ├── routes_predict.py             # /api/predict/bearing, armor, turbofan
│   │   │   └── routes_ws.py                  # /api/ws/telemetry (WebSocket generator)
│   │   ├── ml/                               # Machine Learning & Explainable AI
│   │   │   ├── ai4i.pkl                      # AI4I 2020 Ground Armor failure model
│   │   │   ├── bearing.pkl                   # NASA IMS Bearing rotary model
│   │   │   ├── failure_model.pkl             # NASA N-CMAPSS Turbofan degradation model
│   │   │   ├── predictor.py                  # Model registry & vectorized batch inference
│   │   │   └── explainer.py                  # Explainable AI (XAI) feature attributions
│   │   ├── data/                             # Data and persistent storage
│   │   │   ├── defense_telemetry.db          # Auto-initialized SQLite database
│   │   │   ├── uploads/                      # Raw uploaded CSV telemetry batches
│   │   │   └── scored_batches/               # Exportable scored CSV batches
│   │   ├── database.py                       # SQLite schema, queries, and seed data
│   │   ├── main.py                           # FastAPI application entrypoint & CORS
│   │   ├── requirements.txt                  # Python dependencies manifest
│   │   ├── .env                              # Active environment configuration
│   │   ├── test_features.py                  # 5-Feature automated integration test
│   │   ├── test_chat.py                      # Bilingual Copilot conversation test
│   │   └── test_bearing_mapping.py           # NASA IMS Bearing mapping verification
│   │
│   ├── frontend/                             # React 19 Client (Vite)
│   │   ├── src/
│   │   │   ├── components/
│   │   │   │   ├── Assets/                   # AssetTable, AssetDetail, Gauges
│   │   │   │   ├── Chat/                     # Copilot Chat drawer & message cards
│   │   │   │   ├── Dashboard/                # Fleet status KPIs & critical alerts
│   │   │   │   ├── MaintenancePlan/          # Work order dispatch & scheduling table
│   │   │   │   └── Navbar.jsx                # Tactical header navigation
│   │   │   ├── pages/
│   │   │   │   ├── AssetsPage.jsx
│   │   │   │   ├── DashboardPage.jsx
│   │   │   │   └── MaintenancePlanPage.jsx
│   │   │   ├── App.jsx                       # Main client application shell & tabs
│   │   │   ├── index.css                     # Cyber-defense dark mode styling
│   │   │   └── main.jsx                      # React 19 root bootstrap
│   │   ├── package.json                      # Frontend dependencies & scripts
│   │   └── vite.config.js                    # Vite bundler configuration
│   │
│   ├── .env.example                          # Template environment variables
│   └── README.md                             # Source directory overview
│
├── docs/                                     # Comprehensive Project Documentation
│   ├── 01-wireframe.md                       # Screen-by-screen tactical wireframes
│   ├── 02-sitemap.md                         # Application routing and user flows
│   ├── 03-techstack.md                       # Complete technology stack specifications
│   ├── 04-llm-architecture.md               # Copilot LLM & tool-calling architecture
│   ├── 05-project-architecture.md           # This document (system design & source map)
│   ├── architecture.md                       # Formal technical architecture & Mermaid flow
│   ├── problem-statement.md                  # Defense domain problem statement
│   ├── solution-overview.md                  # Executive solution summary & capabilities
│   ├── setup-guide.md                        # Step-by-step local setup and run instructions
│   └── template-guide.md                     # Hackathon submission guideline reference
│
├── demo/                                     # Demonstration Artifacts
│   ├── screenshots/                          # Application screenshots
│   └── demo-video-link.txt                   # Recorded demonstration video URL
│
├── presentation/                             # Presentation Deck (slides.pdf / slides.pptx)
├── submission.yaml                           # Structured evaluation metadata
├── CONTRIBUTING.md                           # Hackathon rules & instructions
├── .gitignore                                # Git ignore filters
└── README.md                                 # Primary repository entrypoint
```

## End-to-End Data Flow

1. **Live Telemetry & Anomaly Worker:**
   - Background worker in `routes_ws.py` streams live sensor readings (vibration, pressure, temperature) via WebSocket to the React frontend.
   - Operators can inject simulated mechanical spikes to observe real-time gauge changes and Copilot alarm notifications.

2. **Vectorized Multi-Model Batch Scoring:**
   - Uploaded CSV files flow to `routes_batch.py` and are scored in parallel across thousands of rows via `predictor.py` utilizing `bearing.pkl`, `ai4i.pkl`, and `failure_model.pkl`.
   - Results are written to SQLite and saved to disk for export.

3. **Explainable AI (XAI) Attribution:**
   - Telemetry deviations are parsed by `explainer.py` to calculate exact mathematical percentage contributions of anomaly factors, rendering transparent root-cause insights.

4. **Copilot Conversational Intelligence:**
   - User inputs in English or Hinglish are processed by `routes_chat.py`, contextualized with live asset telemetry, and synthesized via Groq LPU (Llama 3.3 70B) or local defense RAG.
   - Actionable intents automatically trigger `save_work_order` to create structured work orders in `defense_telemetry.db`.
