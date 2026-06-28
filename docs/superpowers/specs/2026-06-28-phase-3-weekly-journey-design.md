# Phase 3 Weekly Journey Design

## Overview

Phase 3 turns the Journey tab from placeholder into the first planning loop: create a weekly draft, add focus sessions, ask Lumora for a suggested week, preview the plan, and accept it. The accepted journey becomes the source for Today in Phase 4.

This phase keeps AI suggestion preview-only. The backend does not write scheduled dates or times during suggestion. It persists the schedule only when the user accepts a preview.

## Requirements

- Authenticated users can create weekly journey drafts.
- Users can create multiple drafts for the same week.
- Only one journey can be active per user and week.
- Users can add focus sessions to a draft with title, optional note, category, priority, and estimated duration.
- Users can request a suggested weekly schedule grouped by day.
- Suggested schedules are preview-only until accepted.
- Accepting a draft writes scheduled date/time to its sessions and marks the draft active.
- Accepting one draft deactivates any other active journey for that user/week.
- Every journey/session endpoint validates ownership server-side.
- AI/provider failure returns a usable fallback plan and never leaks raw provider errors.

## Non-Goals

- No full draft manager UI: no compare, delete, rename, duplicate, or version history.
- No manual drag-and-drop calendar editing.
- No persisted suggestion object before accept.
- No real external AI provider requirement in this phase.
- No Today execution behavior beyond providing accepted journey data for Phase 4.

## Evaluated Approaches

| Approach | Pros | Cons | Decision |
| --- | --- | --- | --- |
| One journey per week | Simplest API and UX; easiest tests. | Does not support user preference for multiple drafts. | Rejected. |
| Multiple drafts, one active, preview-only suggestion | Supports experimentation while keeping persistence simple. Clear accept boundary. | Needs active-state conflict handling on accept. | Selected. |
| Full draft management with persisted suggestions | Most flexible; supports resume/compare later. | Too much state and UI for MVP Phase 3. | Rejected. |

## Data Model

### `weekly_journeys`

Fields:

- `id`: string UUID.
- `user_id`: owner FK.
- `week_start`: date, Monday-based.
- `title`: optional calm label.
- `status`: `draft`, `active`, `archived`.
- `created_at`: timestamp.
- `accepted_at`: nullable timestamp.

Rules:

- Multiple `draft` rows can share `user_id + week_start`.
- At most one `active` row should exist for `user_id + week_start`.
- Accepting a draft should archive any currently active journey for the same user/week before activating the accepted draft.

### `focus_sessions`

Fields:

- `id`: string UUID.
- `user_id`: owner FK for direct ownership checks.
- `journey_id`: parent FK.
- `title`: required, trimmed, short.
- `note`: optional.
- `category`: simple string enum-like value for MVP.
- `priority`: `low`, `medium`, `high`.
- `estimated_minutes`: positive integer.
- `scheduled_date`: nullable date.
- `scheduled_time`: nullable `HH:mm` string or time type.
- `status`: `todo`, `scheduled`, `completed`, `skipped`.
- `created_at`: timestamp.

Rules:

- New sessions start as `todo` with no schedule.
- Accept writes schedule and changes accepted sessions to `scheduled`.
- Phase 4 owns completion/skip transitions.

## API Design

### `POST /journeys`

Creates a draft journey for the requested week.

Request:

```json
{
  "weekStart": "2026-06-29",
  "title": "A focused, calm week"
}
```

Response: journey summary with `status: "draft"`.

### `GET /journeys/current`

Returns the active journey for the current week if one exists. If none exists, returns the newest draft for the current week. If none exists, returns `journey: null`.

Rationale: avoids draft list scope while letting the user return to the latest planning draft.

### `POST /journeys/{journey_id}/sessions`

Adds a focus session to an owned draft journey.

Validation:

- Reject if journey is not owned.
- Reject if journey is already `active` or `archived`.
- Reject blank title.
- Reject non-positive or excessive duration.

### `POST /journeys/{journey_id}/suggest`

Returns preview schedule only. It does not update session rows.

Response:

```json
{
  "source": "fallback",
  "days": [
    {
      "date": "2026-06-29",
      "sessions": [
        {
          "sessionId": "session_id",
          "suggestedTime": "09:00",
          "reason": "Start with high-focus work while energy is fresh."
        }
      ]
    }
  ]
}
```

### `POST /journeys/{journey_id}/accept`

Accepts a client-submitted preview and persists schedule.

Request:

```json
{
  "days": [
    {
      "date": "2026-06-29",
      "sessions": [
        {
          "sessionId": "session_id",
          "suggestedTime": "09:00"
        }
      ]
    }
  ]
}
```

Server validation:

- Every `sessionId` belongs to the accepted journey and current user.
- Dates fall within the journey week.
- Times match `HH:mm`.
- Duplicate session IDs are rejected.
- Empty schedule is rejected.

Response: accepted journey summary with `status: "active"` and `acceptedAt`.

## AI And Fallback Behavior

Phase 3 uses an AI service interface but the default provider is deterministic fallback/mock.

Fallback scheduling rules:

- Sort sessions by priority high to low, then creation order.
- Spread sessions Monday through Friday first, then weekend if needed.
- Prefer morning slots for high priority, late morning/afternoon for medium and low.
- Never schedule outside the journey week.
- Return a short, friendly reason for each suggested time.

Provider failures:

- Log safe internal error only if logging exists.
- Return `source: "fallback"` with a complete usable schedule.
- Client never sees raw provider exception text.

## Frontend Design

Journey tab replaces the placeholder with a single calm planning flow:

1. Empty state: invite user to create this week's journey.
2. Draft state: show journey title/week, add-session form, and preview list.
3. Suggesting state: show loading copy that feels calm, not technical.
4. Preview state: show suggested plan grouped by day, with session title/time/reason.
5. Accepted state: show confirmation and navigate/return affordance to Today.

MVP edit affordances:

- Users can add more sessions before suggesting.
- Users can request suggestion again before accept.
- No drag/drop or day/time manual editing in Phase 3.

Mock mode:

- FE repository keeps a mock journey repository so UI can run without BE.
- Mock behavior should mirror backend fallback rules enough for tests and demos.

## Error Handling

- Missing auth redirects to auth flow through existing guard.
- Empty session list disables suggestion CTA with explanatory copy.
- Network/API failures show friendly retry state.
- Invalid accept payload returns structured error and leaves draft unchanged.
- Ownership failures return 404 or 403 without disclosing foreign resource existence.

## Testing Strategy

Backend tests:

- Create multiple drafts for one week.
- Add session only to owned draft.
- Reject access to another user's journey/session.
- Suggest returns grouped fallback plan without mutating sessions.
- Accept persists schedule and marks journey active.
- Accepting second draft archives previous active journey for same user/week.
- Invalid accept payload does not partially update sessions.

Frontend tests:

- Journey screen renders empty/draft/preview states.
- Add session validates required title and duration.
- Suggest preview grouped by day appears.
- Accept transitions to accepted state and can return to Today.
- API errors leave user on safe retryable state.

Verification commands:

- FE: `flutter analyze` and relevant widget/controller tests.
- BE: `python3 -m unittest tests.test_auth tests.test_journeys tests.test_foundation` and `python3 -m py_compile app/main.py`.

## Risks

- Multiple drafts can confuse users if there is no draft list. Mitigation: `current` returns newest draft until a draft manager exists.
- One-active-per-week needs transaction-safe handling. MVP can update existing active journey before activating the selected draft in one DB transaction.
- Preview-only accept trusts client-submitted schedule shape. Mitigation: validate ownership, week bounds, time format, duplicates, and session membership before writing.
- Fallback schedule may be simplistic. Acceptable for MVP; tone and consistency matter more than optimization.

## Success Metrics

- A new authenticated user can create a draft, add at least one session, preview a suggested week, and accept it.
- Accepted journey data is available for the Today flow in Phase 4.
- User cannot read or mutate another user's journey/session.
- AI failure still produces a complete fallback schedule.
- Tests cover the selected draft/active behavior.

## Next Steps

- Update implementation plan for Phase 3 with separate Brain, BE, FE, integration, and verification tasks.
- Update API contract during implementation if exact request/response names change.
- Keep Phase 3 limited to planning and accepting; leave execution, completion, reflection, and review for later phases.
