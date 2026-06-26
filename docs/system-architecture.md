# Lumora System Architecture

## Overview

Lumora is split into three repositories with clear boundaries: `lumora_brain` for planning and contracts, `lumora_fe` for the Flutter mobile app, and `lumora_be` for the FastAPI backend. MVP delivery should happen as vertical slices so each milestone produces a testable user flow.

## Repository Responsibilities

| Repo | Responsibility | Avoid |
| --- | --- | --- |
| `lumora_brain` | Product docs, architecture notes, API contracts, design guidelines, phase plans, release notes. | Runtime app code. |
| `lumora_fe` | Flutter UI, navigation, presentation state, local session/token handling, API integration, mobile build config. | AI prompts, database rules, server-side validation. |
| `lumora_be` | Auth, persistence, API validation, business logic, AI orchestration, review aggregation, deployment readiness. | Pixel-perfect mobile UI and navigation logic. |

## Frontend Architecture

Recommended stack:

- Flutter and Dart.
- Riverpod for state management.
- go_router for navigation.
- Dio for API calls.

Initial app modules:

- Auth: welcome, login, register, session guard.
- Today: hero focus card, daily timeline, session detail, completion flow.
- Journey: weekly planning, focus session form, AI suggestion preview, accept flow.
- Reflection: AI/fallback question, short input, mood, save/skip.
- Review: weekly summary, mood signal, insight, recommendation.
- Settings: profile, preferences, logout.

Frontend should support mock mode so UX work can continue while backend endpoints are incomplete.

## Backend Architecture

Recommended stack:

- FastAPI and Python.
- PostgreSQL.
- SQLAlchemy.
- Alembic migrations.
- JWT authentication.

Initial backend modules:

- Config/env management.
- Database/session management.
- Auth and current-user dependency.
- Users and settings.
- Journeys and focus sessions.
- Reflections and mood.
- Weekly review aggregation.
- AI service interface with mock/fallback provider.

## Data Ownership

- Users own journeys, focus sessions, reflections, reviews, and settings.
- Every user-owned endpoint must validate ownership server-side.
- Frontend state is not a source of truth for security decisions.
- AI output is advisory and must be validated before being stored or returned.

## AI Boundary

AI behavior belongs behind backend service interfaces:

- Weekly schedule suggestion.
- Reflection question generation.
- Weekly insight and recommendation.

Each AI flow must support:

- Mock provider for development.
- Rule-based fallback for provider failure.
- Output validation before client response.
- No raw provider errors exposed to the frontend.

## Local Development Flow

1. Start backend locally and verify `GET /health`.
2. Start frontend in mock mode if backend is unavailable.
3. Switch frontend to API mode when the relevant endpoint slice is ready.
4. Demo each milestone end-to-end before moving to the next major phase.

## Deployment Direction

Railway is the default staging target for MVP unless implementation discovers a concrete blocker. Phase 8 should add deployment config, environment documentation, and a staging smoke-test checklist for Railway.

## Verification Standards

- Frontend: run `flutter analyze` from `/Users/ducnh/Documents/projects/lumora/lumora_fe`.
- Backend syntax: run `python3 -m py_compile app/main.py` from `/Users/ducnh/Documents/projects/lumora/lumora_be`.
- Backend runtime: run FastAPI locally and check health/API docs once dependencies are installed.

## References

- `documents/02_FE_Architecture_Implementation.docx`
- `documents/03_BE_Architecture_API.docx`
- `documents/Lumora_FE_BE_Phase_Plan.docx`
- `docs/development-setup.md`
- `plans/260625-2055-lumora-mvp-phase-plan/plan.md`
