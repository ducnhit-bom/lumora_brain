# Lumora Design Guidelines

## Overview

Lumora should feel like a calm luxury self-growth companion. The interface should make weekly planning and reflection feel light, warm, and personal. Avoid dense productivity tooling, enterprise dashboards, developer-tool aesthetics, and aggressive gamification.

## Design Principles

- Calm over busy.
- Editorial over dashboard.
- Gentle guidance over productivity pressure.
- One primary action per screen where possible.
- Short copy, warm tone, no guilt language.
- Cards and spacing should create breathing room.
- Empty states should feel restorative, not punitive.

## Visual Direction

| Token Area | Direction |
| --- | --- |
| Background | Warm ivory or soft off-white |
| Accent | Champagne/gold, used sparingly |
| Text | High-contrast warm neutral |
| Cards | Soft surfaces with subtle borders/shadows |
| Typography | Premium editorial feel, readable at mobile sizes |
| Motion | Gentle transitions only; no distracting effects |

## Screens

### Welcome/Auth

- Set emotional tone immediately: calm, premium, supportive.
- Avoid generic productivity claims.
- Forms should be simple, with friendly validation messages.

### Today

- Hero focus card is the visual anchor.
- Show only today's relevant sessions.
- Completion should feel rewarding but restrained.
- No-session state should encourage recovery or planning, not shame.

### Weekly Journey

- Planning should feel like arranging a meaningful week, not managing tickets.
- AI suggestion should display as a weekly rhythm grouped by day.
- Editing affordances should be lightweight for MVP.

### Reflection

- Reflection should feel like a luxury notebook.
- One AI/fallback question only.
- One short input only.
- Mood choices: energized, balanced, challenged.
- Save and skip must both feel acceptable.

### Weekly Review

- Review should be supportive and insight-led.
- Avoid KPI-heavy charts.
- Summary cards may show completion count, reflection count, and mood signal.
- Recommendation should feel actionable and kind.

### Settings

- Keep controls basic and uncluttered.
- Prioritize preferences that affect core MVP behavior.
- Notification settings can be placeholder if push is deferred.

## Copy Tone

Use:

- "A focused start for today"
- "What felt meaningful about this session?"
- "You made space for steady progress."
- "Plan next week with a little more breathing room."

Avoid:

- "You failed to complete"
- "Productivity score"
- "Tasks overdue"
- "Optimize your output"

## Accessibility Baseline

- Maintain readable text sizes.
- Keep contrast sufficient for primary text and actions.
- Touch targets should be comfortable on mobile.
- Important buttons and inputs should have semantic labels where practical.
- Do not rely on color alone for session status or mood.

## Anti-Patterns

- Dense task tables.
- Kanban-style productivity boards.
- Heavy analytics dashboards.
- Developer-tool color palettes.
- Excessive gradients or glassmorphism.
- Overly playful gamification.
- AI chat taking over the core flow.

## References

- `documents/04_UI_UX_Mockup_Spec.docx`
- `documents/Lumora_FE_BE_Phase_Plan.docx`
- `plans/260625-2055-lumora-mvp-phase-plan/plan.md`
