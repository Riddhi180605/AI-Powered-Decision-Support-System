# Deploy to Render (Using render.yaml)

This project is already configured with a Render Blueprint in [render.yaml](render.yaml).

## 1) Push your repo

1. Commit your latest changes (including [render.yaml](render.yaml)).
2. Push to GitHub.

## 2) Create service from Blueprint

1. Open Render dashboard.
2. Click New +.
3. Click Blueprint.
4. Connect your GitHub account/repository.
5. Select this repo.
6. Render will detect [render.yaml](render.yaml) automatically.
7. Click Apply.

## 3) Set required environment variables

In the created web service, open Environment and set:

1. OPENAI_API_KEY = your real key (if using OpenAI)
2. GROQ_API_KEY = your real key (if using Groq)
3. LLM_PROVIDER = openai or groq (choose one)
4. PYTHON_VERSION = 3.11.9

Notes:

1. Keep only the provider you actually use in LLM_PROVIDER.
2. If your service URL is different, update ALLOWED_ORIGINS to your real Render URL and redeploy.

## 4) Confirm build/start commands

These are already defined in [render.yaml](render.yaml):

1. Build: pip install --upgrade pip && pip install -r requirements.txt
2. Start: uvicorn backend.main:app --host 0.0.0.0 --port $PORT
3. Health check: /api/health

## 5) Verify deployment

After deploy completes:

1. Open your Render service URL (frontend should load).
2. Check health endpoint: /api/health
3. Upload a small CSV from the UI to verify backend + frontend integration.

## 6) Common issues

1. Build fails on dependencies:
   Use Starter/Standard plan if free tier memory is too low for heavy ML libs.
2. 503 or startup timeout:
   Recheck start command and ensure port uses $PORT.
3. CORS errors:
   Ensure ALLOWED_ORIGINS matches your exact Render URL.
4. AI endpoints fail:
   Verify API key exists and LLM_PROVIDER matches that key.

## 7) Security reminder

If any API keys were ever committed or exposed, rotate them and set fresh keys only in Render environment variables.
