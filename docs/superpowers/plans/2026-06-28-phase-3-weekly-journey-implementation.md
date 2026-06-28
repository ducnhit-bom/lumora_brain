# Phase 3 Weekly Journey Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build Weekly Journey Planning so authenticated users can create draft journeys, add focus sessions, preview a fallback AI schedule, and accept one active journey per week.

**Architecture:** Backend owns journey/session persistence, ownership validation, fallback suggestion, and accept-state transitions. Frontend owns the Journey tab flow with a mock/API repository boundary matching the auth pattern. Brain docs track contract/status changes.

**Tech Stack:** FastAPI, SQLAlchemy, Alembic, Python unittest, Flutter, Riverpod, go_router, Dio.

## Global Constraints

- Use DB-backed opaque bearer auth already implemented in Phase 2.
- Multiple draft journeys are allowed per user/week.
- Only one active journey is allowed per user/week.
- Suggestion is preview-only; accept persists schedule.
- No full draft manager, drag/drop editing, real AI provider, or Today execution behavior in Phase 3.
- All user-owned BE endpoints validate ownership.
- Use TDD: failing tests first, then minimal implementation.

---

## File Structure

- BE create `app/models/journey.py`: `WeeklyJourney` and `FocusSession` models.
- BE modify `app/models/__init__.py`: export journey models for metadata/migrations.
- BE create `app/api/journeys.py`: Pydantic schemas, endpoints, ownership checks, fallback suggestion, accept transition.
- BE modify `app/main.py`: include journeys router.
- BE create `alembic/versions/20260628_0003_journeys.py`: journeys/sessions migration.
- BE create `tests/test_journeys.py`: API tests for drafts, ownership, suggest, accept.
- FE create `lib/features/journey/journey_repository.dart`: mock/API repository and DTOs.
- FE create `lib/features/journey/journey_controller.dart`: Riverpod state/controller.
- FE create `lib/features/journey/journey_screen.dart`: Journey UI flow.
- FE modify `lib/app/router.dart`: route `/journey` to `JourneyScreen`.
- FE create `test/journey_controller_test.dart`: controller tests.
- FE modify `test/widget_test.dart`: route/UI smoke tests.
- Brain modify `docs/api-contracts.md`: align `archived` status and accept request body.
- Brain modify master phase plan: mark Phase 3 completed after verification.
- Brain add journal `docs/journals/260628-phase-3-weekly-journey.md` after implementation.

## Task 1: Backend Journey Persistence And API

**Files:**

- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/tests/test_journeys.py`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/models/journey.py`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/models/__init__.py`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/alembic/versions/20260628_0003_journeys.py`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/api/journeys.py`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/main.py`

**Interfaces:**

- Consumes: `get_current_user` from `app.api.auth`; `api_error` from `app.core.errors`; `get_db` from `app.core.database`.
- Produces: protected endpoints `POST /journeys`, `GET /journeys/current`, `POST /journeys/{journey_id}/sessions`, `POST /journeys/{journey_id}/suggest`, `POST /journeys/{journey_id}/accept`.

- [ ] Step 1: Write failing `tests/test_journeys.py` covering draft creation, multiple drafts, session add, ownership rejection, preview-only suggest, accept, active archival, invalid accept rollback.
- [ ] Step 2: Run `python3 -m unittest tests.test_journeys` and verify import/endpoint failures.
- [ ] Step 3: Add models/migration/router with minimal fallback suggestion and accept transaction.
- [ ] Step 4: Run `python3 -m unittest tests.test_journeys tests.test_auth tests.test_foundation` and `python3 -m py_compile app/main.py`.
- [ ] Step 5: Review API responses for structured errors and no raw provider/internal errors.

## Task 2: Frontend Journey Flow

**Files:**

- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/journey/journey_repository.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/journey/journey_controller.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/journey/journey_screen.dart`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/app/router.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/test/journey_controller_test.dart`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/test/widget_test.dart`

**Interfaces:**

- Consumes: existing `appConfigProvider`, `dioProvider`, auth state, shared cards/buttons/error widgets.
- Produces: `journeyControllerProvider`, `JourneyScreen`, mock/API journey repositories.

- [ ] Step 1: Write failing controller/widget tests for create draft, add session validation, suggest preview, accept, API error safety.
- [ ] Step 2: Run `flutter test test/journey_controller_test.dart test/widget_test.dart` and verify failures.
- [ ] Step 3: Implement repository DTOs and mock/API repository.
- [ ] Step 4: Implement controller state transitions.
- [ ] Step 5: Implement Journey screen and route replacement.
- [ ] Step 6: Run `flutter analyze` and `flutter test test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart`.

## Task 3: Brain Contract, Phase Status, Journal

**Files:**

- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/docs/api-contracts.md`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/plans/260625-2055-lumora-mvp-phase-plan/plan.md`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/docs/journals/260628-phase-3-weekly-journey.md`

**Interfaces:**

- Consumes: implemented backend request/response shapes.
- Produces: updated source of truth for Phase 3 behavior and status.

- [ ] Step 1: Update API contract with `archived` status and accept request payload.
- [ ] Step 2: Mark Phase 3 checkboxes complete only after FE/BE verification passes.
- [ ] Step 3: Add journal summarizing implementation, verification, and deferred risks.
- [ ] Step 4: Run `git diff --check` in brain repo.

## Verification Gate

- BE: `python3 -m unittest tests.test_journeys tests.test_auth tests.test_foundation`
- BE: `python3 -m py_compile app/main.py`
- FE: `flutter analyze`
- FE: `flutter test test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart`
- Brain: `git diff --check`

## Commit Plan

- BE commit: `feat(journeys): add weekly journey planning api`
- FE commit: `feat(journeys): add weekly planning flow`
- Brain commit: `docs: complete phase 3 weekly journey plan`

## Self-Review

- Spec coverage: all selected requirements map to Tasks 1-3.
- Placeholder scan: no TBD/TODO/later placeholders.
- Type consistency: API names match current contract, with intentional `archived` status update.
