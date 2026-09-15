# Tech Stack — Mission Readiness & Predictive Maintenance Copilot

## Frontend Layer
| Layer | Choice | Purpose / Rationale |
|---|---|---|
| **Framework** | React 19 (Vite) | Blazing fast HMR, modern hooks, lightweight footprint, instant UI rendering. |
| **Icons & UI Elements** | Lucide React | High-tech tactical cyber-defense icons, indicators, and status glyphs. |
| **Styling** | Cyber-Glassmorphism CSS | High-contrast dark mode military telemetry styling with responsive glassmorphism panels. |
| **Live Visualizations** | Custom SVG Radial Gauges & Live Stream Charts | Real-time 60 FPS sensor telemetry gauges for vibration, pressure, and temperature. |
| **Networking & Real-Time** | Native Fetch API + WebSocket API | Persistent bi-directional channel to `ws://localhost:8000/api/ws/telemetry` and REST API endpoints. |

## Backend & API Layer
| Layer | Choice | Purpose / Rationale |
|---|---|---|
| **Runtime & Framework** | Python 3.11+ / FastAPI | High-performance asynchronous Python web framework with native Pydantic v2 data validation and Swagger/OpenAPI generation. |
| **Server Engine** | Uvicorn (ASGI) | Lightning-fast async server capable of handling hundreds of concurrent telemetry WebSocket connections. |
| **API Architecture** | Modular REST + WebSockets | Decoupled routers for Assets (`/api/assets`), Predictions (`/api/predict`), Batch Uploads (`/api/upload-csv`), and Chat (`/api/chat`). |
| **Concurrency Model** | `asyncio` Background Tasks | Dedicated background telemetry generator task feeding real-time drift and spikes to connected clients. |

## Machine Learning & AI Inference
| Component | Model / Technology | Dataset & Purpose |
|---|---|---|
| **Rotary & Aviation Transmissions** | Random Forest / Gradient Boosted Regressor (`bearing.pkl`) | **NASA IMS Bearing Dataset:** High-frequency vibration, kurtosis, and peak frequency features for bearing fatigue and RUL prediction. |
| **Ground Combat Armor & Vehicles** | Ensemble Classifier (`ai4i.pkl`) | **AI4I 2020 Predictive Maintenance Dataset:** Tool wear, process/air temperatures, torque, and rotational speed modeling for heavy armor (Arjun MBT, T-90). |
| **Turbofan Propulsion Systems** | Degradation Regressor (`failure_model.pkl`) | **NASA N-CMAPSS Turbofan Dataset:** Multichannel sensor degradation sequences estimating remaining cycles/days to failure. |
| **Explainable AI (XAI)** | Custom Feature Attribution Engine (`ml/explainer.py`) | Computes relative percentage contributions for out-of-spec sensor readings to explain physical root causes. |
| **Serving & Vectorization** | NumPy, pandas, joblib | High-throughput vectorized matrix calculations enabling 5,000+ batch rows scored in < 1 second. |

## Generative AI Copilot & Agent Layer
| Component | Technology | Purpose |
|---|---|---|
| **Primary LLM Engine** | Groq LPU (Llama 3.3 70B Versatile) | Ultra-fast (<300ms) token generation, contextual military reasoning, and autonomous action planning. |
| **Secondary Fallback LLM** | Google Gemini API (`gemini-1.5-flash`) | Automatic fallback provider when configured in `.env`. |
| **Air-Gapped / Local RAG** | Deterministic Rule-Based Defense RAG | Fully functional offline reasoning engine for zero-connectivity edge deployments. |
| **Language Capability** | English & Hinglish (Hindi + English) | Domain-tailored system prompt supporting mixed military maintenance terminology. |
| **Autonomous Action Dispatch** | Structured Tool Calling (`routes_chat.py`) | Automatically generates and commits formal maintenance Work Orders into the SQLite database. |

## Datastore & Persistence
| Component | Choice | Purpose |
|---|---|---|
| **Primary Database** | Embedded SQLite (`data/defense_telemetry.db`) | Zero-configuration, file-based persistence for registered assets, batch runs, and work orders. |
| **File Storage** | Local Disk Storage (`data/uploads/`, `data/scored_batches/`) | Stores uploaded raw CSV telemetry files and exportable scored outputs. |
| **Schema Structure** | Tables: `custom_assets`, `telemetry_batches`, `work_orders` | Structured relational tables with JSON columns for flexible time-series telemetry and action plans. |

## Development & Operations
| Component | Choice | Rationale |
|---|---|---|
| **Dev Environment** | IBM Bob IDE / Antigravity IDE | Multi-language workspace with seamless background task execution. |
| **Package Management** | `pip` (Python) + `npm` (Node.js) | Standard, reproducible dependency trees (`requirements.txt`, `package.json`). |
| **Test Automation** | Custom Python test suites (`test_features.py`, `test_chat.py`, `test_bearing_mapping.py`) | End-to-end integration and sanity verification without heavy third-party runners. |
