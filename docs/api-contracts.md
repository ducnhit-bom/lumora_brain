# Lumora API Contracts

## Overview

This document defines the first-pass API contract for Lumora MVP. The backend owns validation, persistence, auth, AI orchestration, and ownership checks. The frontend consumes these endpoints through a typed API client and should handle loading, empty, and error states consistently.

Contracts may change during implementation, but changes that affect FE/BE integration must be reflected here.

## Global Rules

- All protected endpoints require `Authorization: Bearer <token>`.
- Auth tokens are opaque random values. Backend stores only token hashes in `auth_sessions`.
- All timestamps should be ISO 8601 strings.
- Server validates user ownership for every user-owned resource.
- Server should return friendly structured errors, never raw internal exceptions or raw AI provider errors.
- AI endpoints must return fallback responses when provider calls fail.

## Error Shape

```json
{
  "error": {
    "code": "string_code",
    "message": "Human friendly message",
    "details": {}
  }
}
```

Recommended HTTP status usage:

| Status | Use |
| --- | --- |
| 400 | Invalid input or invalid state transition |
| 401 | Missing, invalid, or expired token |
| 403 | Authenticated but not allowed to access resource |
| 404 | Resource not found or not owned by current user |
| 409 | Duplicate or conflicting state |
| 422 | Validation error |
| 500 | Unexpected server error with safe message |

## Auth

### `POST /auth/register`

Request:

```json
{
  "name": "Linh Nguyen",
  "email": "linh@example.com",
  "password": "password"
}
```

Response:

```json
{
  "accessToken": "opaque_access_token",
  "user": {
    "id": "user_id",
    "name": "Linh Nguyen",
    "email": "linh@example.com"
  }
}
```

### `POST /auth/login`

Request:

```json
{
  "email": "linh@example.com",
  "password": "password"
}
```

Response: same shape as register.

### `GET /users/me`

Protected. Returns current user profile.

Response:

```json
{
  "id": "user_id",
  "name": "Linh Nguyen",
  "email": "linh@example.com"
}
```

### `POST /auth/logout`

Protected. Revokes the current database-backed auth session.

Response:

```json
{
  "status": "ok"
}
```

## Journeys

Journey status values: `draft`, `active`, `completed`.

Focus session status values: `todo`, `scheduled`, `completed`, `skipped`.

### `POST /journeys`

Protected. Creates a weekly journey.

Request:

```json
{
  "weekStart": "2026-06-29",
  "title": "A focused, calm week"
}
```

Response:

```json
{
  "id": "journey_id",
  "weekStart": "2026-06-29",
  "status": "draft",
  "title": "A focused, calm week"
}
```

### `GET /journeys/current`

Protected. Returns the current user's active or draft journey for the current week.

Response when journey exists:

```json
{
  "id": "journey_id",
  "weekStart": "2026-06-29",
  "status": "active",
  "title": "A focused, calm week",
  "sessions": [
    {
      "id": "session_id",
      "title": "Write proposal outline",
      "note": "Keep it simple",
      "category": "work",
      "priority": "high",
      "estimatedMinutes": 45,
      "scheduledDate": "2026-06-29",
      "scheduledTime": "09:00",
      "status": "scheduled"
    }
  ]
}
```

Response when no current journey exists:

```json
{
  "journey": null
}
```

### `POST /journeys/{journey_id}/sessions`

Protected. Adds a focus session to a journey.

Request:

```json
{
  "title": "Write proposal outline",
  "note": "Keep it simple",
  "category": "work",
  "priority": "high",
  "estimatedMinutes": 45
}
```

Response:

```json
{
  "id": "session_id",
  "journeyId": "journey_id",
  "title": "Write proposal outline",
  "note": "Keep it simple",
  "category": "work",
  "priority": "high",
  "estimatedMinutes": 45,
  "scheduledDate": null,
  "scheduledTime": null,
  "status": "todo"
}
```

### `POST /journeys/{journey_id}/suggest`

Protected. Returns an AI or fallback weekly schedule grouped by day.

Response:

```json
{
  "source": "ai_or_fallback",
  "days": [
    {
      "date": "2026-06-29",
      "sessions": [
        {
          "sessionId": "session_id",
          "suggestedTime": "09:00",
          "reason": "Best for high-focus work"
        }
      ]
    }
  ]
}
```

### `POST /journeys/{journey_id}/accept`

Protected. Accepts the suggested schedule and marks the journey active.

Response:

```json
{
  "id": "journey_id",
  "status": "active",
  "acceptedAt": "2026-06-26T09:30:00Z"
}
```

## Sessions

### `GET /sessions/today`

Protected. Returns today's sessions for current user.

Response:

```json
{
  "date": "2026-06-29",
  "sessions": [
    {
      "id": "session_id",
      "journeyId": "journey_id",
      "title": "Write proposal outline",
      "note": "Keep it simple",
      "category": "work",
      "priority": "high",
      "estimatedMinutes": 45,
      "scheduledTime": "09:00",
      "status": "scheduled"
    }
  ]
}
```

### `GET /sessions/{session_id}`

Protected. Returns session detail if owned by current user.

Response:

```json
{
  "id": "session_id",
  "journeyId": "journey_id",
  "title": "Write proposal outline",
  "note": "Keep it simple",
  "category": "work",
  "priority": "high",
  "estimatedMinutes": 45,
  "scheduledDate": "2026-06-29",
  "scheduledTime": "09:00",
  "status": "scheduled",
  "completedAt": null,
  "skippedAt": null
}
```

### `POST /sessions/{session_id}/complete`

Protected. Completes a session.

Response:

```json
{
  "sessionId": "session_id",
  "status": "completed",
  "completedAt": "2026-06-26T09:30:00Z",
  "openReflection": true
}
```

### `POST /sessions/{session_id}/undo-complete`

Protected. Returns completed session to active/todo state.

Response:

```json
{
  "sessionId": "session_id",
  "status": "scheduled",
  "completedAt": null
}
```

### `POST /sessions/{session_id}/skip`

Protected. Skips a session for the current user.

Response:

```json
{
  "sessionId": "session_id",
  "status": "skipped",
  "skippedAt": "2026-06-26T09:30:00Z"
}
```

## Reflections

### `GET /sessions/{session_id}/reflection-question`

Protected. Returns AI or fallback reflection question.

Response:

```json
{
  "source": "ai_or_fallback",
  "question": "What made this focus session meaningful?"
}
```

### `POST /reflections`

Protected. Saves a short reflection.

Validation:

- `content` is required when calling `POST /reflections`.
- Non-empty reflection content must be at most 500 characters.
- UI should encourage one short line, ideally 280 characters or less.
- `mood` is optional and must be one of `energized`, `balanced`, `challenged` when provided.
- Skipping reflection is a frontend navigation action in MVP and does not create a reflection record.

Request:

```json
{
  "journeyId": "journey_id",
  "sessionId": "session_id",
  "content": "I made a clear first draft.",
  "mood": "balanced"
}
```

Response:

```json
{
  "id": "reflection_id",
  "journeyId": "journey_id",
  "sessionId": "session_id",
  "content": "I made a clear first draft.",
  "mood": "balanced",
  "createdAt": "2026-06-26T09:35:00Z"
}
```

### `GET /journeys/{journey_id}/reflections`

Protected. Lists reflections for a journey.

Response:

```json
{
  "reflections": [
    {
      "id": "reflection_id",
      "sessionId": "session_id",
      "content": "I made a clear first draft.",
      "mood": "balanced",
      "createdAt": "2026-06-26T09:35:00Z"
    }
  ]
}
```

## Weekly Review

### `GET /journeys/{journey_id}/review`

Protected. Returns weekly review summary.

Response:

```json
{
  "journeyId": "journey_id",
  "sessionsCompleted": 6,
  "reflectionCount": 4,
  "moodSummary": {
    "energized": 1,
    "balanced": 2,
    "challenged": 1
  },
  "insight": {
    "source": "ai_or_fallback",
    "text": "You made steady progress by protecting small focus windows."
  },
  "recommendation": "Plan fewer high-priority sessions next week and leave more recovery space."
}
```

Alternative route allowed during implementation: `GET /reviews/weekly/current`, if it simplifies frontend usage. It must return the same response shape as `GET /journeys/{journey_id}/review`.

## Settings

### `GET /settings`

Protected. Returns current user settings.

Response:

```json
{
  "autoOpenReflection": true,
  "preferredFocusTime": "09:00",
  "maxSessionsPerDay": 3,
  "timezone": "Asia/Ho_Chi_Minh"
}
```

### `PATCH /settings`

Protected. Updates basic preferences.

Request:

```json
{
  "autoOpenReflection": true,
  "preferredFocusTime": "09:00",
  "maxSessionsPerDay": 3,
  "timezone": "Asia/Ho_Chi_Minh"
}
```

Response: same shape as `GET /settings`.

## Finalized Phase 0 Defaults

- ORM: SQLAlchemy.
- Staging deployment target: Railway.
- Skip session endpoint: included in MVP.
- Reflection max length: 500 characters.
- Skipped reflections: not persisted in MVP.
- Auth token strategy: opaque DB-backed bearer token, hash stored in Supabase Postgres.

## References

- `documents/03_BE_Architecture_API.docx`
- `documents/Lumora_FE_BE_Phase_Plan.docx`
- `plans/260625-2055-lumora-mvp-phase-plan/plan.md`
