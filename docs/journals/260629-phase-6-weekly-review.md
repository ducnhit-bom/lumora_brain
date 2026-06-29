# Phase 6 Weekly Review

## Context

Phase 6 completes the first MVP loop after reflection: users can open Review and see a calm summary of completed focus sessions, reflections, mood signal, fallback insight, and a next-week recommendation.

## What Changed

- Backend added `GET /journeys/{journey_id}/review`.
- Backend computes review data on demand from existing `focus_sessions` and `reflections`; no persisted review table was added.
- Backend validates journey ownership and returns `404 not_found` for missing or foreign journeys.
- Backend returns deterministic fallback insight and recommendation instead of using a real AI provider.
- Frontend replaced the Review placeholder with a real `ReviewScreen`.
- Frontend added review repository/controller and mock/API review loading.
- Review UI shows summary cards, mood signal, insight, recommendation, retry state, no-journey empty state, and planning CTA.
- Review UI only renders review data for active journeys; draft/no journey states show the planning empty state.
- Review tab refreshes when selected from the shell so counts do not stay stale after completion/reflection changes.

## Verification

- BE: `python3 -m unittest tests.test_reviews tests.test_reflections tests.test_sessions tests.test_journeys tests.test_auth tests.test_foundation` passed, 41 tests.
- BE: `python3 -m py_compile app/main.py` passed.
- FE: `flutter analyze` passed with no issues.
- FE: `flutter test test/review_controller_test.dart test/reflection_controller_test.dart test/today_controller_test.dart test/journey_controller_test.dart test/auth_controller_test.dart test/widget_test.dart` passed, 34 tests.

## Decisions

- Use `GET /journeys/{journey_id}/review` rather than adding `GET /reviews/weekly/current`.
- Keep Phase 6 fallback-only for insight generation; real AI weekly insight remains deferred.
- Treat no current journey as frontend empty state, not API failure.
- Treat draft current journey as not reviewable in the frontend because Weekly Review is for the current active journey.

## Deferred

- Real AI provider integration for weekly insight.
- Persisted weekly review snapshots.
- Rich historical review browsing.
