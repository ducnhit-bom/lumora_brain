# Deployment

## Platform: Railway

Railway is the default backend staging target for the internal MVP. The backend repo includes `Dockerfile` and `railway.json` so Railway can build and run the FastAPI service from Docker.

## Backend Repo

Path: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be`

## Deploy Command

Use Railway GitHub deployment or Railway CLI from the backend repo after the project is linked:

```bash
railway up
```

## Runtime Command

The Docker image runs:

```bash
alembic upgrade head && uvicorn app.main:app --host 0.0.0.0 --port ${PORT:-8000}
```

## Environment Variables

Required:

- `APP_ENV=staging`
- `DATABASE_URL=postgresql+psycopg://...`
- `CORS_ORIGINS=<comma-separated frontend origins>`

Optional:

- `APP_NAME=Lumora API`
- `LOG_LEVEL=INFO`

## Health Check

Railway health check path:

```text
/health
```

Expected response:

```json
{"status":"ok"}
```

## Smoke Test

After deploy, run the internal beta smoke test listed in `docs/known-issues.md` against the staging API URL.

## Rollback

Use Railway's deployment history to roll back to the previous successful deployment. Database migrations should remain backward-compatible during MVP beta work.
