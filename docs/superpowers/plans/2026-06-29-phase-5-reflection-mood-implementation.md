# Phase 5 Reflection Mood Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build Reflection + Mood so users can answer a short prompt after completing a focus session, optionally select mood, save or skip, then return to Today.

**Architecture:** Backend adds reflection persistence linked to user, journey, and optional session, plus a fallback reflection question endpoint. Frontend adds a Reflection flow and routes completed sessions to it using existing Phase 4 `openReflection` signal. Skip remains frontend-only and does not persist data.

**Tech Stack:** FastAPI, SQLAlchemy, Alembic, Python unittest, Flutter, Riverpod, go_router, Dio.

## Global Constraints

- Use existing DB-backed opaque bearer auth.
- Reflection content max length is 500 characters.
- UI should encourage one short line, ideally 280 characters or less.
- Mood is optional and must be `energized`, `balanced`, or `challenged` when provided.
- Empty reflection skip is frontend navigation only; do not persist skipped reflection.
- All reflection endpoints validate user ownership.
- AI question provider is fallback/mock only in Phase 5; no real AI dependency.
- TDD required: failing tests before implementation.

---

## File Structure

- BE modify `app/models/journey.py`: add `Reflection` model.
- BE modify `app/models/__init__.py`: export `Reflection`.
- BE create `alembic/versions/20260629_0005_reflections.py`: create reflections table.
- BE create `app/api/reflections.py`: question, create, list endpoints.
- BE modify `app/main.py`: include reflections router.
- BE create `tests/test_reflections.py`: ownership, question fallback, save/list, validation.
- FE create `lib/features/reflection/reflection_repository.dart`: DTOs, mock/API repository.
- FE create `lib/features/reflection/reflection_controller.dart`: Riverpod state/actions.
- FE create `lib/features/reflection/reflection_screen.dart`: question, input, mood, save/skip UI.
- FE modify `lib/features/today/today_screen.dart`: route to reflection after completion.
- FE modify `lib/features/today/today_detail_screen.dart`: route to reflection after detail completion.
- FE modify `lib/app/router.dart`: add `/reflections/session/:sessionId` route and replace Review placeholder if needed later.
- FE create `test/reflection_controller_test.dart`: controller tests.
- FE modify `test/widget_test.dart`: reflection route/save/skip smoke tests.
- Brain modify `docs/api-contracts.md`: align exact Phase 5 behavior.
- Brain modify master plan Phase 5 checkboxes/status after verification.
- Brain add journal `docs/journals/260629-phase-5-reflection-mood.md`.

## Task 1: Backend Reflection API

**Files:**

- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/tests/test_reflections.py`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/models/journey.py`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/models/__init__.py`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/alembic/versions/20260629_0005_reflections.py`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/api/reflections.py`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/main.py`

**Interfaces:**

- Consumes: `FocusSession`, `WeeklyJourney`, `get_current_user`, `get_db`, `api_error`.
- Produces: `GET /sessions/{session_id}/reflection-question`, `POST /reflections`, `GET /journeys/{journey_id}/reflections`.

- [x] Step 1: Write failing `tests/test_reflections.py` covering fallback question, save reflection, optional mood, list reflections, max length rejection, invalid mood rejection, and cross-user 404.
- [x] Step 2: Run `python3 -m unittest tests.test_reflections` and verify missing-feature failure.
- [x] Step 3: Add model, migration, reflection router, ownership validation, fallback question.
- [x] Step 4: Run `python3 -m unittest tests.test_reflections tests.test_sessions tests.test_journeys tests.test_auth tests.test_foundation`.
- [x] Step 5: Run `python3 -m py_compile app/main.py`.

## Task 2: Frontend Reflection Flow

**Files:**

- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/reflection/reflection_repository.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/reflection/reflection_controller.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/reflection/reflection_screen.dart`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/today/today_screen.dart`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/today/today_detail_screen.dart`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/app/router.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/test/reflection_controller_test.dart`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/test/widget_test.dart`

**Interfaces:**

- Consumes: `todayControllerProvider.openReflectionSessionId`, existing `dioProvider`, shared widgets.
- Produces: `reflectionControllerProvider`, `ReflectionScreen`, mock/API reflection repository.

- [x] Step 1: Write failing controller tests for load question, save reflection with mood, validation error for >500 chars, skip state.
- [x] Step 2: Write failing widget tests for reflection route, save, skip returns Today.
- [x] Step 3: Implement repository DTOs and mock/API repository.
- [x] Step 4: Implement controller state transitions and validation.
- [x] Step 5: Implement Reflection screen and Today completion navigation.
- [x] Step 6: Run `flutter analyze` and `flutter test test/reflection_controller_test.dart test/today_controller_test.dart test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart`.

## Task 3: Brain Docs And Phase Sync

**Files:**

- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/docs/api-contracts.md`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/plans/260625-2055-lumora-mvp-phase-plan/plan.md`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/docs/journals/260629-phase-5-reflection-mood.md`

**Interfaces:**

- Consumes: implemented BE/FE behavior.
- Produces: updated Phase 5 trace and API contract.

- [x] Step 1: Update reflection API docs if exact response names change.
- [x] Step 2: Mark Phase 5 checkboxes complete only after FE/BE verification passes.
- [x] Step 3: Add journal with implementation, verification, and deferred AI/provider scope.
- [x] Step 4: Run `git diff --check` in brain repo.

## Verification Gate

- BE: `python3 -m unittest tests.test_reflections tests.test_sessions tests.test_journeys tests.test_auth tests.test_foundation`
- BE: `python3 -m py_compile app/main.py`
- FE: `flutter analyze`
- FE: `flutter test test/reflection_controller_test.dart test/today_controller_test.dart test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart`
- Brain: `git diff --check`

## Commit Plan

- BE commit: `feat(reflections): add reflection capture api`
- FE commit: `feat(reflections): add reflection mood flow`
- Brain commit: `docs: complete phase 5 reflection mood plan`

## Self-Review

- Spec coverage: Phase 5 FE/BE/integration/verification checklist maps to Tasks 1-3.
- Placeholder scan: no TBD/TODO/fill-later instructions.
- Type consistency: endpoint names match current API contract and master plan.
