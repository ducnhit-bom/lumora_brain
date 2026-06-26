# AGENTS.md

This file provides guidance to OpenCode when working in the Lumora brain repo.

## Repo Role

**Name:** lumora_brain
**Purpose:** Product thinking, requirements, UX notes, architecture decisions, and shared planning documents for Lumora.

## Read First

- Check `./documents/` for current product and architecture references.

Important source documents currently include:

- `./documents/01_PRD_Weekly_AI_Journal.docx`
- `./documents/02_FE_Architecture_Implementation.docx`
- `./documents/03_BE_Architecture_API.docx`
- `./documents/04_UI_UX_Mockup_Spec.docx`
- `./documents/Lumora_FE_BE_Phase_Plan.docx`

## Cross-Repo Links

- `./lumora_fe` is a symlink to `../lumora_fe`.
- `./lumora_be` is a symlink to `../lumora_be`.
- Use these links for quick reference only. Prefer editing files through their real repo paths.

## Brain Repo Rules

- Keep product decisions, API contracts, UI specs, and implementation plans here.
- When changing FE or BE behavior, update or create the relevant planning note here if the decision affects both repos.
- Do not place app runtime code in this repo unless explicitly requested.
- If converting `.docx` content into markdown, preserve the source document name and make the generated markdown easy to diff.

## Agent Behavior

- Clarify product intent before coding when requirements are ambiguous.
- Produce concise specs with explicit assumptions, open questions, and acceptance criteria.
- For implementation plans, split work by repo: FE tasks, BE tasks, integration tasks, verification tasks.
