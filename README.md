# 🎯 Visual Quiz Game — Django Web App

A hand-gesture quiz game built with Django, Django Channels, and MediaPipe Tasks Vision JS.

## Features

- 🖐 **Hand Tracking** — Select answers by pointing at zones (MediaPipe JS, runs in browser)
- ⌨️ **Keyboard Fallback** — Press A/B/C/D keys
- 🏆 **Live Leaderboard** — Real-time updates via WebSocket (Django Channels)
- 🔧 **Admin Panel** — Add/manage questions, bulk upload CSV/Excel
- 📊 **Grade & Review** — A+ to F grade with per-question review

---

## Quick Start (Local)

```bash
# 1. Clone and setup
git clone <your-repo>
cd visual_quiz_django
python -m venv venv && source venv/bin/activate   # Windows: venv\Scripts\activate

# 2. Install dependencies
pip install -r requirements.txt

# 3. Configure environment
cp .env.example .env
# Edit .env if needed (SQLite + InMemory channels work out of the box)

# 4. Run migrations + seed data
python manage.py migrate
python manage.py createsuperuser
python manage.py seed_questions

# 5. Start server
daphne quiz_game.asgi:application
# Visit http://localhost:8000
```

---

## Deploy to Render.com

### Option A — render.yaml (Infrastructure as Code)

1. Push this repo to GitHub
2. Go to [render.com](https://render.com) → New → Blueprint
3. Connect your GitHub repo — Render reads `render.yaml` automatically
4. Click **Apply** — it creates the web service, PostgreSQL, and Redis in one step
5. After deploy, run seed data via Render Shell:
   ```
   python manage.py seed_questions
   python manage.py createsuperuser
   ```

### Option B — Manual Setup

1. Create a **Web Service** on Render:
   - Runtime: Python 3
   - Build Command: `pip install -r requirements.txt && python manage.py collectstatic --noinput && python manage.py migrate`
   - Start Command: `daphne -b 0.0.0.0 -p $PORT quiz_game.asgi:application`

2. Add environment variables:
   ```
   SECRET_KEY         = <generate a strong random key>
   DEBUG              = False
   ALLOWED_HOSTS      = your-app.onrender.com
   DATABASE_URL       = (from your Render Postgres instance)
   REDIS_URL          = (from your Render Redis instance, optional)
   ```

3. Create **PostgreSQL** database → copy connection string → set `DATABASE_URL`
4. (Optional) Create **Redis** instance → set `REDIS_URL` for live leaderboard

---

## Project Structure

```
visual_quiz_django/
├── quiz_game/          # Django project settings & ASGI config
├── quiz/               # Core app: models, views, consumers, admin
│   ├── templates/quiz/ # All HTML templates
│   └── management/     # seed_questions management command
├── accounts/           # Auth (login/logout)
├── templates/          # Shared base templates
├── static/             # CSS, JS
├── requirements.txt
├── render.yaml         # Render deployment config
└── manage.py
```

## CSV Upload Format

| Column | Required | Notes |
|--------|----------|-------|
| question | Yes | Question text |
| choice_a / b / c / d | Yes | Answer choices |
| correct | Yes | A, B, C, or D |
| category | Yes | Must match existing category |
| difficulty | No | Easy / Medium / Hard |
| time_limit | No | Seconds (default: 30) |
| explanation | No | Shown after answer |

Download the template from Admin Panel → Bulk Upload → Download CSV Template.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | Django 5.x |
| WebSocket | Django Channels 4.x |
| ASGI Server | Daphne |
| Hand Tracking | MediaPipe Tasks Vision JS (browser) |
| Database (dev) | SQLite |
| Database (prod) | PostgreSQL |
| Cache/Channels | Redis |
| Static Files | WhiteNoise |
| Deployment | Render.com |
