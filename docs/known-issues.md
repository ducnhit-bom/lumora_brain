# Known Issues

## Internal MVP Beta

- Auth token is kept in frontend session state only. Users may need to log in again after app restart until secure persistence is added.
- Weekly planning, reflection questions, and weekly insights use deterministic fallback behavior only. No external AI provider is connected yet.
- Push notification settings are not active because push delivery is out of MVP scope.
- Offline mode and retry queues are not implemented. Network failures show local error states and require manual retry.
- Android release signing still uses Flutter's debug signing config for local release runs. Production signing must be configured before public distribution.
- iOS code signing and provisioning are not finalized for TestFlight/App Store distribution.
- Staging deployment config exists for backend, but a live Railway project URL is not recorded yet.
- Rate limiting is not implemented. Current AI-cost risk is low because all AI-like behavior is fallback-only.
- Crash reporting and analytics are not configured.

## Beta Smoke Test

Before sharing an internal build, run this flow against the intended API environment:

1. Register a new user.
2. Create a weekly journey.
3. Add at least one focus session.
4. Request and accept the fallback weekly schedule.
5. Open Today and complete a scheduled session.
6. Save a one-line reflection with mood.
7. Open Weekly Review and confirm summary/insight render.
8. Open Settings, update one preference, then log out.
