# Soccer Pickup Organizer

Local-first web app to organize pickup soccer games: **React** + **Tailwind** on the frontend, **FastAPI** + **PostgreSQL** on the backend. This repo is being built in phases (scaffold → database → auth → games API → UI → polish).

## Prerequisites

- **Docker Desktop** (or Docker Engine + Compose) — for PostgreSQL locally  
- **Python 3.11+** — backend  
- **Node.js 20+** and **npm** — frontend (Vite)

## Repository layout

```
Pickup/
  docker-compose.yml    # PostgreSQL (add at repo root when you wire the DB)
  backend/              # FastAPI app
  frontend/             # Vite + React + Tailwind
  README.md
```

## 1. PostgreSQL (Docker)

From the repository root, create `docker-compose.yml` if it does not exist yet, then start the database:

```yaml
services:
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: pickup
      POSTGRES_PASSWORD: pickup
      POSTGRES_DB: pickup
    ports:
      - "5432:5432"
    volumes:
      - pickup_pgdata:/var/lib/postgresql/data

volumes:
  pickup_pgdata:
```

```powershell
docker compose up -d
```

Stop the database when you are done:

```powershell
docker compose down
```

Connection string for later phases (`.env` in `backend/`):

```text
DATABASE_URL=postgresql+psycopg://pickup:pickup@localhost:5432/pickup
```

## 2. Backend (FastAPI)

```powershell
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Run the API (adjust the module path if your app lives in a package, e.g. `app.main:app`):

```powershell
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

- API base URL: [http://localhost:8000](http://localhost:8000)  
- Interactive docs (Swagger): [http://localhost:8000/docs](http://localhost:8000/docs)

The frontend dev server runs on port **5173**, so configure **CORS** in FastAPI to allow `http://localhost:5173` once the UI calls the API.

## 3. Frontend (Vite + React + Tailwind)

Scaffold the app once (from the repository root), if `frontend/` is still empty:

```powershell
npm create vite@latest frontend -- --template react-ts
cd frontend
npm install
npm install -D tailwindcss @tailwindcss/vite
```

Follow the current [Tailwind + Vite](https://tailwindcss.com/docs/installation/using-vite) docs to add Tailwind to the Vite project, then:

```powershell
npm run dev
```

- App URL: [http://localhost:5173](http://localhost:5173)

## Ports

| Service    | Port  |
| ---------- | ----- |
| PostgreSQL | 5432  |
| FastAPI    | 8000  |
| Vite dev   | 5173  |

## Troubleshooting

- **CORS errors in the browser** — Ensure the backend allows origin `http://localhost:5173` and the frontend uses the correct API base URL (`http://localhost:8000`).  
- **Port already in use** — Stop the other process or change the port in `uvicorn` / Vite config.  
- **Docker does not start** — Confirm Docker Desktop is running on Windows.

## Roadmap (phases)

1. Scaffold — this repo layout, Docker Postgres, runnable API + frontend  
2. Database — SQLAlchemy models, Alembic migrations, session dependency  
3. Auth — register/login, JWT, password hashing  
4. Games API — CRUD, join/leave, max players  
5. React UI — auth pages, game list/create/detail  
6. Polish — validation, loading/error/empty states  

QR / “join at the spot” is deferred until the core flow works end-to-end.
