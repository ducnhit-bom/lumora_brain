# Lumora Development Setup

## Overview

Local development uses separate FE and BE repos. Keep `lumora_brain` for shared docs and contracts only.

## Local Ports

| Service | Default URL |
| --- | --- |
| Backend API | `http://127.0.0.1:8000` |
| Backend health | `http://127.0.0.1:8000/health` |
| Backend docs | `http://127.0.0.1:8000/docs` |
| Flutter app | Device, simulator, emulator, or web target selected by `flutter run` |

## Frontend

```bash
cd /Users/ducnh/Documents/projects/lumora/lumora_fe
flutter pub get
flutter run
```

Checks:

```bash
flutter analyze
flutter test
```

Mock mode is enabled by default in `lib/core/config/app_environment.dart` so the app shell can run without the backend.

## Backend

```bash
cd /Users/ducnh/Documents/projects/lumora/lumora_be
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

Checks:

```bash
python3 -m py_compile app/main.py
python3 -m unittest tests.test_foundation
```

## References

- `docs/system-architecture.md`
- `docs/api-contracts.md`
- `plans/260625-2055-lumora-mvp-phase-plan/plan.md`
