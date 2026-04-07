# AI-Powered SDSS

An end-to-end AI-powered Statistical Decision Support System (SDSS) for:

- Dataset upload and profiling
- Automated data cleaning recommendations
- Multi-model machine learning training and comparison
- Explainable and LLM-assisted business insights
- Retrieval-augmented analytics chatbot
- What-if simulation for decision testing

The project is implemented as a single FastAPI service that also serves the frontend pages.

## Core Features

1. Data ingestion
- Upload CSV or Excel files.
- Automatic schema and quality summary (rows, columns, missing values, duplicates, data types).

2. Intelligent cleaning
- Duplicate removal suggestions.
- Missing value strategy suggestions (delete/fill with mean, median, or mode).
- Download cleaned dataset as CSV.

3. ML training and model selection
- Supports classification and regression flows.
- Trains multiple models and compares metrics.
- Identifies the best model automatically.
- Export best trained model as a pickle bundle.

4. AI analytics
- LLM-backed recommendations and narrative insights.
- SHAP-based feature impact interpretation.
- Context-aware business guidance by dataset domain.

5. RAG chatbot
- Builds a vector index from project artifacts.
- Answers questions using retrieved project context.

6. What-if simulation
- Modify feature values and compare predicted outcomes.
- Generate explanation for impact of proposed changes.

## Tech Stack

- Backend: FastAPI, Uvicorn
- Data and ML: pandas, numpy, scikit-learn, xgboost, shap
- LLM/AI: openai SDK (OpenAI-compatible), groq
- RAG: sentence-transformers, FAISS (faiss-cpu)
- Frontend: HTML, CSS, vanilla JavaScript

## Project Structure

```text
AI-Powered-SDSS/
  backend/
    main.py
    ai_advisor.py
    rag_chatbot.py
    what_if_simulator.py
    test_ai_advisor.py
  frontend/
    index.html
    dataset.html
    cleaning.html
    cleaning-results.html
    ml-training.html
    suggestions.html
    css/
    js/
  requirements.txt
  render.yaml
  DEPLOY_RENDER.md
```

## How It Runs

The app runs as one integrated service:

- API routes are served by FastAPI.
- Frontend static files are mounted at root `/`.
- Opening the service URL loads the web UI.

## Local Development

### 1) Prerequisites

- Python 3.11 (recommended: 3.11.9)
- pip

### 2) Install dependencies

Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install -r requirements.txt
```

macOS/Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

### 3) Configure environment variables

Create a `.env` file in project root (or `backend/.env`).

Minimal example:

```env
APP_HOST=localhost
APP_PORT=8000
ALLOWED_ORIGINS=http://localhost:8000
AUTO_OPEN_FRONTEND=0

LLM_PROVIDER=openai
OPENAI_API_KEY=your_key_here
```

### 4) Start the app

```bash
uvicorn backend.main:app --reload
```

Open:

- http://127.0.0.1:8000

Health check:

- http://127.0.0.1:8000/api/health

## Frontend API URL Resolution

Frontend scripts resolve API base URL in this order:

1. `localStorage.SDSS_API_URL` (if present)
2. `http://localhost:8001` when page is opened as `file://`
3. `window.location.origin` otherwise

For integrated deployment (same host for UI and API), no manual API URL override is needed.

## Main API Endpoints

### Health and workflow

- `GET /api/health`
- `GET /project-state`

### Data upload and cleaning

- `POST /upload`
- `POST /cleaning-suggestions`
- `POST /clean`
- `GET /download-cleaned`

### ML training and model export

- `POST /train-ml`
- `GET /download-best-model`

### AI suggestions and insights

- `POST /get-ai-suggestions`
- `POST /generate-ai-insights`

### Chatbot and RAG

- `POST /chatbot/build-index`
- `POST /chatbot/query`
- `POST /chatbot/answer-question`

### What-if simulation

- `POST /what-if-config`
- `POST /simulate-what-if`

## Environment Variables Reference

### App server

- `APP_HOST` default: `localhost`
- `APP_PORT` default: `8001`
- `ALLOWED_ORIGINS` comma-separated origins, default includes localhost ports
- `AUTO_OPEN_FRONTEND` values `1/true/yes` to auto-open browser locally

### LLM configuration

- `LLM_PROVIDER` values: `openai` or `groq`
- `OPENAI_API_KEY` required when provider is openai
- `GROQ_API_KEY` required when provider is groq
- `LLM_MODEL` optional single preferred model
- `LLM_MODELS` optional comma-separated fallback models
- `LLM_BASE_URL` optional custom OpenAI-compatible base URL

## Deployment on Render

This repository already includes a Render Blueprint file:

- `render.yaml`

Current Render config:

- Build command: `pip install --upgrade pip && pip install -r requirements.txt`
- Start command: `uvicorn backend.main:app --host 0.0.0.0 --port $PORT`
- Health check: `/api/health`

Step-by-step guide:

- See `DEPLOY_RENDER.md`

## Operational Notes

- This app stores active workflow data in process memory and temp directories.
- Uploaded/cleaned datasets and trained artifacts are not persistent across restarts unless you add external storage.
- Heavy ML/AI dependencies can require more memory and longer cold starts on small hosting plans.

## Security Notes

- Never commit real API keys.
- If keys were exposed, rotate them immediately and set fresh values in your host secret manager.

## Future Improvements

- Add persistent storage for datasets/models.
- Add authentication and per-user sessions.
- Add request validation schemas for all endpoint payloads.
- Add CI checks and API tests.
