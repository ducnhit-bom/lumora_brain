# Phase 5 Reflection Mood

## Context

Phase 5 replaces the Phase 4 reflection placeholder with a real post-completion reflection flow. The MVP goal is intentionally light: one fallback question, one short reflection input, optional mood, save or skip, then return to Today.

## What Changed

- Backend added `Reflection` persistence linked to user, journey, and focus session.
- Backend added `GET /sessions/{session_id}/reflection-question`, `POST /reflections`, and `GET /journeys/{journey_id}/reflections`.
- Backend validates ownership, content length up to 500 characters, and mood values: `energized`, `balanced`, `challenged`.
- Backend only accepts reflections for completed sessions and avoids reopening Reflection on re-completion when a session already has a reflection.
- Frontend added Reflection repository, controller, screen, and route `/reflections/session/:sessionId`.
- Today and session detail completion now open Reflection when completion returns `openReflection: true`.
- Save and skip both return to Today; skip remains frontend-only and does not persist a record.
- Brain API contract and MVP plan now reflect the implemented Phase 5 behavior.

## Verification

- BE: `python3 -m unittest tests.test_reflections tests.test_sessions tests.test_journeys tests.test_auth tests.test_foundation` passed, 35 tests.
- BE: `python3 -m py_compile app/main.py` passed.
- FE: `flutter analyze` passed with no issues.
- FE: `flutter test test/reflection_controller_test.dart test/today_controller_test.dart test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart` passed, 27 tests.

## Decisions

- `POST /reflections` accepts `sessionId`, `content`, and optional `mood`; `journeyId` is derived server-side from the owned session.
- A saved reflection is one-per-session in MVP; undo/re-complete does not create a second reflection prompt.
- Reflection question generation is deterministic fallback in Phase 5; real AI/provider behavior remains deferred.
- One reflection per session is enforced with a unique session constraint.

## Deferred

- Real AI-personalized reflection questions.
- Weekly Review aggregation and mood summaries in Phase 6.
- More advanced edit/delete reflection flows.
