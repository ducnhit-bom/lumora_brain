# Phase 6 Weekly Review Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build Weekly Review so users can see a supportive summary, mood signal, fallback insight, and recommendation for the current active journey.

**Architecture:** Backend computes review data on demand from existing journey, session, and reflection rows. Frontend replaces the Review placeholder with a Riverpod-driven Review feature that loads the current journey and then calls the review API. No persisted review table or real AI provider is introduced in this phase.

**Tech Stack:** FastAPI, SQLAlchemy, Python unittest, Flutter, Riverpod, go_router, Dio.

## Global Constraints

- Use `GET /journeys/{journey_id}/review` as the backend review endpoint.
- Validate ownership for review data and return `404 not_found` for missing or foreign journeys.
- No real AI provider in Phase 6; return deterministic fallback insight.
- Avoid KPI-heavy charts; Review UI should be supportive and insight-led.
- Keep mock mode working offline.
- TDD required: write failing tests before implementation.

---

## File Structure

- BE create `app/api/reviews.py`: review schemas, aggregation endpoint, fallback insight/recommendation.
- BE modify `app/main.py`: include reviews router.
- BE create `tests/test_reviews.py`: review endpoint coverage.
- FE create `lib/features/review/review_repository.dart`: API/mock repository and DTOs.
- FE create `lib/features/review/review_controller.dart`: review loading state and actions.
- FE create `lib/features/review/review_screen.dart`: Review tab UI.
- FE modify `lib/app/router.dart`: replace Review placeholder with `ReviewScreen`.
- FE create `test/review_controller_test.dart`: controller coverage.
- FE modify `test/widget_test.dart`: Review tab widget coverage.
- Brain modify `docs/api-contracts.md` if exact response wording changes.
- Brain modify master plan Phase 6 checkboxes/status after verification.
- Brain create `docs/journals/260629-phase-6-weekly-review.md`.

## Task 1: Backend Review API

**Files:**

- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/tests/test_reviews.py`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/api/reviews.py`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_be/lumora_be/app/main.py`

**Interfaces:**

- Consumes: `WeeklyJourney`, `FocusSession`, `Reflection`, `get_current_user`, `get_db`, `api_error`.
- Produces: `GET /journeys/{journey_id}/review` returning `journeyId`, `sessionsCompleted`, `reflectionCount`, `moodSummary`, `insight`, `recommendation`.

- [x] Step 1: Write failing `tests/test_reviews.py` with these behaviors:
  - owned journey review returns zero counts and supportive fallback when no data exists;
  - completed sessions are counted;
  - saved reflections are counted;
  - moods are counted into `energized`, `balanced`, `challenged`;
  - foreign user gets `404` with `error.code == "not_found"`.
- [x] Step 2: Run `python3 -m unittest tests.test_reviews` from backend repo. Expected: fails because route/module is missing.
- [x] Step 3: Create `app/api/reviews.py` with local Pydantic response models and route-local helpers matching existing API style.
- [x] Step 4: Implement ownership check using `db.get(WeeklyJourney, journey_id)` and `journey.user_id == user.id`.
- [x] Step 5: Implement aggregation using SQLAlchemy queries scoped by `journey.id` and `user.id`.
- [x] Step 6: Implement deterministic fallback insight:
  - zero completed and zero reflections: source `fallback`, text `Your week is ready for a gentle first reflection when you have more data.`
  - challenged count greater than energized + balanced: text `This week asked for more effort than ease. You still kept showing up.`
  - otherwise: text `You made steady progress by protecting small focus windows.`
- [x] Step 7: Implement recommendation:
  - zero data: `Complete one focus session and write one short reflection to unlock a richer weekly review.`
  - challenged-heavy: `Plan next week with fewer high-priority sessions and a little more recovery space.`
  - otherwise: `Plan next week with one clear high-priority focus and a little more breathing room.`
- [x] Step 8: Include the router in `app/main.py`.
- [x] Step 9: Run `python3 -m unittest tests.test_reviews tests.test_reflections tests.test_sessions tests.test_journeys tests.test_auth tests.test_foundation`.
- [x] Step 10: Run `python3 -m py_compile app/main.py`.

## Task 2: Frontend Review Flow

**Files:**

- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/review/review_repository.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/review/review_controller.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/features/review/review_screen.dart`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/lib/app/router.dart`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/test/review_controller_test.dart`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_fe/lumora_fe/test/widget_test.dart`

**Interfaces:**

- Consumes: `journeyRepositoryProvider.currentJourney()`, `dioProvider`, shared `LumoraCard`, `LumoraButton`.
- Produces: `reviewRepositoryProvider`, `reviewControllerProvider`, `ReviewScreen`.

- [x] Step 1: Write failing `test/review_controller_test.dart` covering:
  - no current journey produces `hasNoJourney == true` and no error;
  - current journey loads review data;
  - repository failure produces friendly error `Weekly review could not be loaded. Please try again.`.
- [x] Step 2: Write failing `test/widget_test.dart` coverage for:
  - Review tab shows review summary and insight when authenticated;
  - Review tab shows no-journey empty state and `Plan this week` CTA when no current journey exists.
- [x] Step 3: Run `flutter test test/review_controller_test.dart test/widget_test.dart`. Expected: fails because Review feature files and route replacement are missing.
- [x] Step 4: Implement `review_repository.dart` with `WeeklyReview`, `MoodSummary`, `ReviewInsight`, `ReviewRepository`, `MockReviewRepository`, and `ApiReviewRepository`.
- [x] Step 5: Implement `review_controller.dart` with `ReviewState` and `ReviewController.load()` that first loads current journey, then review.
- [x] Step 6: Implement `review_screen.dart` with calm summary cards, mood signal, insight, recommendation, retry state, and CTA to `/journey`.
- [x] Step 7: Replace `/review` placeholder in `app/router.dart` with `ReviewScreen`.
- [x] Step 8: Run `flutter analyze`.
- [x] Step 9: Run `flutter test test/review_controller_test.dart test/reflection_controller_test.dart test/today_controller_test.dart test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart`.

## Task 3: Brain Docs And Phase Sync

**Files:**

- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/docs/api-contracts.md`
- Modify: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/plans/260625-2055-lumora-mvp-phase-plan/plan.md`
- Create: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/docs/journals/260629-phase-6-weekly-review.md`
- Existing spec: `/Users/ducnh/Documents/projects/lumora/lumora_brain/lumora_brain/docs/superpowers/specs/2026-06-29-phase-6-weekly-review-design.md`

**Interfaces:**

- Consumes: implemented BE/FE behavior.
- Produces: updated Phase 6 status and implementation journal.

- [x] Step 1: Update API contract only if implementation response differs from current `Weekly Review` section.
- [x] Step 2: Mark Phase 6 tasks complete after FE/BE verification passes.
- [x] Step 3: Add journal summarizing implementation, review fixes, verification, and deferred real AI provider scope.
- [x] Step 4: Run `git diff --check` in brain repo.

## Verification Gate

- BE: `python3 -m unittest tests.test_reviews tests.test_reflections tests.test_sessions tests.test_journeys tests.test_auth tests.test_foundation`
- BE: `python3 -m py_compile app/main.py`
- FE: `flutter analyze`
- FE: `flutter test test/review_controller_test.dart test/reflection_controller_test.dart test/today_controller_test.dart test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart`
- Brain: `git diff --check`

## Commit Plan

- BE commit: `feat(reviews): add weekly review api`
- FE commit: `feat(review): add weekly review flow`
- Brain commit: `docs: complete phase 6 weekly review plan`

## Self-Review

- Spec coverage: backend aggregation, frontend review tab, empty states, tests, and docs are mapped to Tasks 1-3.
- Placeholder scan: no TBD/TODO/fill-later markers.
- Type consistency: endpoint and response names match `docs/api-contracts.md`.
