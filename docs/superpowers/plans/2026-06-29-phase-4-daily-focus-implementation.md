# Phase 4 Daily Focus Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build Daily Focus Execution so users can view today's scheduled sessions, open session detail, complete, undo-complete, and skip focus sessions.

**Architecture:** Backend adds session execution endpoints on top of Phase 3 `FocusSession` rows. Frontend replaces the Today placeholder with a Riverpod-driven Today flow that consumes mock/API session repositories. Phase 4 only records execution state; Reflection UI remains Phase 5.

**Tech Stack:** FastAPI, SQLAlchemy, Alembic, Python unittest, Flutter, Riverpod, go_router, Dio.

## Global Constraints

- Use existing DB-backed opaque bearer auth.
- Use existing `focus_sessions` rows from accepted journeys; no new execution table.
- User-owned endpoints must validate `FocusSession.user_id`.
- Complete/undo/skip affect only owned sessions.
- Completion response returns `openReflection: true` but does not create reflection data in Phase 4.
- Keep Today UI calm and minimal; no task-board density.
- TDD required: failing tests before implementation.

---

## File Structure

- BE modify `app/models/journey.py`: add `completed_at` and `skipped_at` nullable timestamps.
- BE create `alembic/versions/20260629_0004_session_execution.py`: add completion/skip timestamp columns.
- BE create `app/api/sessions.py`: today/detail/complete/undo/skip endpoints.
- BE modify `app/main.py`: include sessions router.
- BE create `tests/test_sessions.py`: ownership, today list, detail, transitions, idempotency safety.
- FE create `lib/features/today/today_repository.dart`: DTOs, mock/API implementations.
- FE create `lib/features/today/today_controller.dart`: Riverpod state transitions.
- FE create `lib/features/today/today_screen.dart`: Today list/detail/actions.
- FE modify `lib/app/router.dart`: route `/today` to `TodayScreen`.
- FE create `test/today_controller_test.dart`: controller tests.
- FE modify `test/widget_test.dart`: Today screen and action smoke tests.
- Brain modify `docs/api-contracts.md`: align exact session transition behavior.
- Brain modify master plan Phase 4 checkboxes/status after verification.
- Brain add journal `docs/journals/260629-phase-4-daily-focus.md`.

## Task 1: Backend Session Execution API

**Files:**

- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/tests/test_sessions.py`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/models/journey.py`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/alembic/versions/20260629_0004_session_execution.py`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/api/sessions.py`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/main.py`

**Interfaces:**

- Consumes: `FocusSession`, `WeeklyJourney`, `get_current_user`, `get_db`, `api_error`.
- Produces: `GET /sessions/today`, `GET /sessions/{session_id}`, `POST /sessions/{session_id}/complete`, `POST /sessions/{session_id}/undo-complete`, `POST /sessions/{session_id}/skip`.

- [ ] Step 1: Write failing `tests/test_sessions.py` covering today list from active accepted journey, detail ownership, complete, duplicate complete rejection, undo, skip, and cross-user 404.
- [ ] Step 2: Run `python3 -m unittest tests.test_sessions` and verify feature-missing failure.
- [ ] Step 3: Add model timestamps, migration, sessions router, and register router in `app/main.py`.
- [ ] Step 4: Run `python3 -m unittest tests.test_sessions tests.test_journeys tests.test_auth tests.test_foundation`.
- [ ] Step 5: Run `python3 -m py_compile app/main.py`.

## Task 2: Frontend Today Flow

**Files:**

- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/today/today_repository.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/today/today_controller.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/today/today_screen.dart`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/app/router.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/test/today_controller_test.dart`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/test/widget_test.dart`

**Interfaces:**

- Consumes: existing `appConfigProvider`, `dioProvider`, shared widgets, bottom tab shell.
- Produces: `todayControllerProvider`, `TodayScreen`, mock/API session repository.

- [ ] Step 1: Write failing controller tests for load today, complete, duplicate/error recovery, undo, skip.
- [ ] Step 2: Write failing widget tests for Today empty/scheduled/complete states.
- [ ] Step 3: Implement repository DTOs and mock/API repository.
- [ ] Step 4: Implement controller state transitions and safe error messages.
- [ ] Step 5: Implement Today screen and route replacement.
- [ ] Step 6: Run `flutter analyze` and `flutter test test/today_controller_test.dart test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart`.

## Task 3: Brain Docs And Phase Sync

**Files:**

- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/docs/api-contracts.md`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/plans/260625-2055-lumora-mvp-phase-plan/plan.md`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/docs/journals/260629-phase-4-daily-focus.md`

**Interfaces:**

- Consumes: implemented BE/FE behavior.
- Produces: updated contract and Phase 4 completion trace.

- [ ] Step 1: Update session API docs with duplicate complete behavior and skip/undo response shapes if changed.
- [ ] Step 2: Mark Phase 4 checkboxes complete only after FE/BE verification passes.
- [ ] Step 3: Add journal with implementation, verification, and deferred Reflection scope.
- [ ] Step 4: Run `git diff --check` in brain repo.

## Verification Gate

- BE: `python3 -m unittest tests.test_sessions tests.test_journeys tests.test_auth tests.test_foundation`
- BE: `python3 -m py_compile app/main.py`
- FE: `flutter analyze`
- FE: `flutter test test/today_controller_test.dart test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart`
- Brain: `git diff --check`

## Commit Plan

- BE commit: `feat(sessions): add daily focus execution api`
- FE commit: `feat(today): add daily focus flow`
- Brain commit: `docs: complete phase 4 daily focus plan`

## Self-Review

- Spec coverage: Phase 4 FE/BE/integration/verification checklist maps to Tasks 1-3.
- Placeholder scan: no TBD/TODO/fill-later instructions.
- Type consistency: endpoint names match current API contract and master plan.
