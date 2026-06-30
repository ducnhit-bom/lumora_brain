# Lumora Internal MVP Release Notes

## 0.1.0 Internal Beta

### Scope

- Email/password registration, login, session-backed auth, and logout.
- Weekly journey planning with focus sessions, fallback AI schedule suggestion, and accept flow.
- Daily focus execution with Today list, detail view, complete, undo, and skip.
- One-line reflection after completion with optional mood.
- Weekly review with completion summary, mood summary, fallback insight, and recommendation.
- Settings screen with profile email, auto-open reflection preference, preferred focus time, max sessions/day, and logout.

### Backend

- FastAPI API with PostgreSQL/Supabase via SQLAlchemy and Alembic.
- DB-backed opaque bearer tokens stored as hashes.
- Ownership validation for user-owned journeys, sessions, reflections, reviews, and settings.
- Rule-based fallback behavior for planning, reflection question, and weekly review.
- Railway-ready Docker deployment config with request logging.

### Frontend

- Flutter app shell with Today, Journey, Review, and Settings tabs.
- Riverpod state management and Dio API integration.
- Mock mode remains available for local development.
- Android application id and iOS bundle id set to `com.lumora.app`.

### Verification Snapshot

- Backend: 48 tests passing.
- Frontend: `flutter analyze` clean and 39 tests passing.
- Core MVP flow covered by backend integration tests and frontend widget/controller tests. A manual staging smoke test is still required before sharing an internal build.

### Not Included Yet

- Real AI provider integration.
- Push notifications.
- Calendar sync.
- Secure persisted auth token across app restart.
- Production app signing, crash reporting, analytics, or public store distribution.
