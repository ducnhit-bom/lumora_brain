# Phase 3 Weekly Journey Journal

## Context

Implemented Lumora MVP Phase 3: Weekly Journey Planning across backend and frontend. Phase 3 follows the approved design: multiple draft journeys per week, preview-only suggestion, and one active journey per user/week after accept.

## What Changed

- Backend added `weekly_journeys` and `focus_sessions` persistence with Alembic migration.
- Backend added protected journey endpoints for create, current, add session, suggest, and accept.
- Suggestion is deterministic fallback/mock and does not mutate sessions before accept.
- Accept validates schedule ownership, week bounds, duplicate sessions, and time format before persisting.
- Accepting a second draft archives the previous active journey for that user/week.
- Frontend replaced Journey placeholder with a planning flow: create draft, add focus, preview suggestion, accept journey.
- Frontend added mock/API journey repository and Riverpod controller tests.
- Brain docs now align journey status with `archived` and document the accept request payload.

## Verification

- Backend: `python3 -m unittest tests.test_journeys tests.test_auth tests.test_foundation` passed.
- Backend: `python3 -m py_compile app/main.py` passed.
- Frontend: `flutter analyze` passed.
- Frontend: `flutter test test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart` passed.

## Decisions

- Kept suggestion preview-only to avoid persisted draft suggestion state.
- Used deterministic fallback suggestion as the Phase 3 provider; real AI provider remains out of scope.
- Kept draft management minimal: current endpoint returns current-week active journey first, then newest draft.
- Used `archived` for previously active journey when accepting another draft in the same week.

## Next

- Phase 4 should consume accepted journey data for Today sessions.
- Phase 4 should own complete, undo, and skip session transitions.
- Before beta, revisit secure token persistence and optional draft management if user testing shows confusion.
