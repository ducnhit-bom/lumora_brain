# Lumora Product Overview

## Overview

Lumora is a premium weekly planning, daily focus, reflection, and AI insight mobile app. The MVP validates one core loop: plan a meaningful week, complete daily focus sessions, write a short reflection, and receive a supportive weekly insight.

The product should feel calm, personal, and encouraging. It is not a task manager, project management tool, analytics dashboard, or AI chat app.

## Target Users

| Persona | Need | Lumora Response |
| --- | --- | --- |
| Busy self-growth user | Wants intentional weekly planning without heavy productivity tooling. | Weekly journey planning with a simple focus-session model. |
| Reflective professional | Wants a lightweight habit for noticing progress. | One-line reflection and optional mood after session completion. |
| AI-curious planner | Wants helpful guidance without complex setup. | AI-suggested weekly plan and weekly insight with fallback behavior. |

## MVP Loop

1. User registers or logs in.
2. User creates the current weekly journey.
3. User adds focus sessions that matter this week.
4. Lumora suggests a weekly schedule using AI or mock/fallback logic.
5. User accepts the suggested journey.
6. User opens Today and completes, skips, or undoes daily focus sessions.
7. Completion opens a short reflection with an AI/fallback question and optional mood.
8. User reviews the week with summary, mood signal, AI insight, and recommendation.

## MVP Scope

### Included

- Email/password authentication.
- Weekly journey planning.
- Focus session creation and daily execution.
- AI-suggested weekly plan with mock-first and fallback support.
- Short reflection after session completion.
- Optional mood selection: energized, balanced, challenged.
- Weekly review with supportive AI or fallback insight.
- Basic settings and preferences.
- Internal beta readiness.

### Excluded

- Google Calendar sync.
- Push notifications.
- Native home widgets.
- Social sharing.
- Long-form AI chat coach.
- Heavy analytics dashboards.
- Monthly or quarterly trend reports.
- Full Figma-to-Flutter automation.

## Product Decisions

| Decision | MVP Value |
| --- | --- |
| App name | Lumora |
| Auth method | Email/password first |
| AI strategy | Provider abstraction, mock-first, fallback required |
| BE ORM | SQLAlchemy |
| Staging target | Railway |
| Week start | Monday |
| Reflection length | Maximum 500 characters; UI should encourage one short line around 280 characters or less |
| Empty reflection | User may skip; skipped reflections are not persisted in MVP |
| Analytics | None or minimal placeholders only |

## Success Metrics

- User can complete the full MVP loop end-to-end in a staging environment.
- User can create and accept a weekly journey without needing calendar sync.
- User can complete a daily focus session and write or skip a reflection.
- Weekly review can display a useful summary even when AI provider is unavailable.
- The experience feels premium, calm, and friendly rather than productivity-heavy.

## Acceptance Criteria

- New user can register, create a weekly journey, add focus sessions, and accept an AI-curated plan.
- Existing user can log in and return to Today with persisted session state.
- User can complete a focus session and optionally reflect with mood.
- User can view a weekly review with completion summary, reflection count, mood summary, insight, and recommendation.
- MVP has clear fallback behavior for AI failures.

## References

- `documents/01_PRD_Weekly_AI_Journal.docx`
- `documents/Lumora_FE_BE_Phase_Plan.docx`
- `plans/260625-2055-lumora-mvp-phase-plan/plan.md`
