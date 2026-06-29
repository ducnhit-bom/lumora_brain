# Phase 6 Weekly Review Design

## Overview

Phase 6 adds a supportive weekly review experience using data already produced by Phases 3-5. The review should feel like a calm summary and next-step note, not an analytics dashboard.

## Requirements

- Review tab replaces the current placeholder.
- User can view review for the current active journey.
- Review shows completed session count, reflection count, mood summary, fallback insight, and recommendation.
- Empty or insufficient data shows supportive copy, not a failure state.
- No real AI provider is required in this phase; use deterministic fallback insight.
- All backend review data must be scoped to the authenticated user.

## Constraints

- Keep MVP simple and avoid heavy charts.
- Preserve existing Flutter/Riverpod repository-controller-screen patterns.
- Preserve existing FastAPI route-local schema/helper pattern.
- Use current API contract route: `GET /journeys/{journey_id}/review`.
- Do not add persisted review rows yet; compute review from journeys, sessions, and reflections.

## Backend Design

- Add `app/api/reviews.py` with `GET /journeys/{journey_id}/review`.
- Validate journey ownership with the same not-found behavior used by journey/reflection APIs.
- Aggregate from existing tables:
  - `sessionsCompleted`: count `FocusSession.status == "completed"` for the journey.
  - `reflectionCount`: count `Reflection` rows for the journey.
  - `moodSummary`: count reflection moods across `energized`, `balanced`, `challenged`, defaulting missing moods to no bucket.
- Return deterministic fallback insight and recommendation:
  - No data: gentle prompt to complete a focus and reflect.
  - More challenged than balanced/energized: recommend fewer high-priority sessions and recovery room.
  - Otherwise: reinforce steady progress and protecting focus blocks.

## Frontend Design

- Add `lib/features/review/` with repository, controller, and screen.
- `ReviewScreen` loads current journey via existing `JourneyRepository.currentJourney()`.
- If no current journey exists, show a calm empty state and CTA to `/journey`.
- If current journey exists, call `ReviewRepository.review(journey.id)`.
- UI sections:
  - Headline: Weekly Review.
  - Summary cards: completed sessions, reflections, strongest mood signal.
  - Insight card: fallback/AI-source text.
  - Recommendation card: actionable and kind next-week suggestion.
  - CTA: Plan next week.
- Mock repository returns deterministic review data for offline UI work.

## API Interface

`GET /journeys/{journey_id}/review`

```json
{
  "journeyId": "journey_id",
  "sessionsCompleted": 2,
  "reflectionCount": 1,
  "moodSummary": {
    "energized": 0,
    "balanced": 1,
    "challenged": 0
  },
  "insight": {
    "source": "fallback",
    "text": "You made steady progress by protecting small focus windows."
  },
  "recommendation": "Plan next week with one clear high-priority focus and a little more breathing room."
}
```

## Error Handling

- Missing/foreign journey returns `404 not_found`.
- Missing auth uses existing auth behavior.
- FE network/API failures show retryable friendly error.
- No current journey is not an error; it is an empty state.

## Testing

- BE tests cover owned review aggregation, empty review, cross-user 404, and mood counts.
- FE controller tests cover no journey, success, and repository failure.
- FE widget tests cover Review tab rendering and no-journey empty state.

## Acceptance Criteria

- User can open Review tab and see weekly summary for current journey.
- Review API uses only current user's owned journey data.
- Reflection count and mood summary reflect saved Phase 5 reflection data.
- Empty state is supportive and non-judgmental.
- `flutter analyze`, targeted Flutter tests, backend unit tests, and backend syntax check pass.

## Self-Review

- Placeholder scan: no TBD/TODO markers.
- Consistency: uses existing journey review API contract and current data model.
- Scope: excludes persisted review rows and real AI provider.
- Ambiguity: no-current-journey and insufficient-data behaviors are explicit.
