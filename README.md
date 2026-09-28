# Personal Finance Advisor Bot

An AI-powered personal finance assistant built with Flask, SQLAlchemy, Jinja2, and
Google Gemini. Track income and expenses, generate AI budgets, get spending
insights, and chat with an AI advisor — all behind user accounts.

## Features

- Secure registration/login (Flask-Login, hashed passwords)
- Income recording and expense tracking with categories + monthly limits
- AI-generated monthly budget plans (Gemini)
- AI spending analysis: overspending detection + cost-optimization tips
- AI chat advisor that answers questions using your real financial data
- Interactive dashboard (Chart.js) and a monthly report view
- Savings goals with progress tracking
- Works with SQLite locally and PostgreSQL in production

## 1. Run it locally

```bash
git clone <your-repo-url>
cd personal-finance-advisor-bot
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env
```

Edit `.env`:
- `SECRET_KEY` — any long random string
- `GEMINI_API_KEY` — get one free at https://aistudio.google.com/app/apikey
  (the app still runs without it — AI features just fall back to simple
  rule-based logic instead of Gemini)

Run it:
```bash
python app.py
```
Visit `http://localhost:5000`, register an account, and go.

## 2. Push it to GitHub

```bash
git init
git add .
git commit -m "Initial commit: Personal Finance Advisor Bot"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

`.env` and the local SQLite file are already excluded via `.gitignore`, so
your secret key and API key won't be committed.

## 3. Deploy it live (Render)

Render gives you a free web service + free Postgres database.

1. Push the code to GitHub (step 2).
2. Go to https://render.com → **New +** → **Blueprint**.
3. Connect your GitHub repo — Render will read `render.yaml` in this project
   and auto-configure the web service and database.
4. When prompted, paste your `GEMINI_API_KEY` (the only value it asks you
   for manually — `SECRET_KEY` and the database URL are generated for you).
5. Click **Apply**. First deploy takes a couple of minutes.
6. Your app will be live at `https://personal-finance-advisor-bot.onrender.com`
   (or whatever name you gave it).

**No `render.yaml` support / prefer manual setup:** New + → Web Service →
connect the repo → Build command `pip install -r requirements.txt` → Start
command `gunicorn app:app` → add the same env vars from `.env.example`
under Environment.

## 4. Alternative: deploy on Railway

1. Go to https://railway.app → **New Project** → **Deploy from GitHub repo**.
2. Add a PostgreSQL plugin (Railway sets `DATABASE_URL` automatically).
3. In the service's Variables tab, add `SECRET_KEY`, `GEMINI_API_KEY`,
   `GEMINI_MODEL`.
4. Railway auto-detects the `Procfile` and runs `gunicorn app:app`.
5. Generate a public domain under Settings → Networking.

## Project structure

```
├── app.py              # App factory + entry point
├── config.py           # Env-based configuration
├── extensions.py       # db, login_manager
├── models.py           # User, Income, Expense, ExpenseCategory, Budget, SavingsGoal
├── auth.py             # Register / login / logout
├── finance.py          # Income, expenses, categories, budget, savings routes
├── dashboard.py        # Dashboard, reports, AI chat routes
├── ai_advisor.py        # Gemini integration + fallback logic
├── templates/          # Jinja2 templates
├── static/css/         # Stylesheet
├── requirements.txt
├── Procfile             # For Render/Railway/Heroku-style hosts
├── render.yaml          # One-click Render blueprint
└── .env.example
```

## Notes

- Default SQLite database is fine for local dev/demo; production deploys
  should use the Postgres `DATABASE_URL` provided by Render/Railway.
- Without a `GEMINI_API_KEY`, the app still works — budgets fall back to a
  standard 50/30/20 split and chat explains that AI isn't configured yet.
- Change `GEMINI_MODEL` in `.env` if Google renames or retires the default
  model — check https://ai.google.dev/gemini-api/docs/models for the
  current list.
