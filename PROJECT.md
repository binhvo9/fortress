# Fortress — AI-Augmented UX Portfolio Project

## What this is

A UI/UX portfolio case study designed to prove three things at once:
1. **UI/UX design mastery** — research, IA, design system, hi-fi UI, prototyping.
2. **Figma mastery** — advanced use of Figma (components, variables, auto-layout, prototyping, dev handoff).
3. **Claude-augmented workflow mastery** — using Claude + Figma MCP as a real design collaborator to move 10x faster, not just as a novelty.

Target audience: recruiters/hiring managers for **AI-Augmented Designer** roles (product design roles at AI-native companies/startups that value designers who can operate AI tooling, not just use Figma).

## The product concept: Fortress

A personal wealth-building & rental-property management app. Helps someone building a "financial fortress" — tracking rental properties (esp. phòng trọ / boarding rooms), tenants, rent collection, cashflow, ROI per property, and net worth trajectory toward financial freedom. AI-assisted insights (rent optimization, risk flags, freedom-timeline projection).

Why this concept:
- **Authentic** — based on the designer's real financial goals (build income-generating real estate, reach financial freedom). Real problem, real user (self).
- **Visually rich** — dashboards, charts, timelines → good surface for showing UI craft (ties into `dataviz` skill principles when building screens).
- **Scoped** — one clear domain, not a kitchen-sink app.

## Deliverable

- A polished Figma file (design system + wireframes + hi-fi screens + interactive prototype).
- A Behance-style case study (or similar) telling the story, including a section on the Claude+Figma MCP-assisted workflow.
- **No production code required** — Figma + case study only.

## Workflow (how Claude should operate on this project)

- Claude works **one feature/task at a time**, each defined by a spec file in [`specs/`](specs/).
- Track current + completed work in [`CURRENT-FEATURES.md`](CURRENT-FEATURES.md) — newest completed entry goes at the **bottom** of the history.
- Don't start a new spec until the current one is marked done.
- Capture Figma MCP findings (what works, what doesn't) in [`figma-mcp-capabilities.md`](figma-mcp-capabilities.md) as they're discovered — internal reference, not polished.
- Log meaningful design decisions in [`design-log.md`](design-log.md) — raw material for writing the case study later.
- User-stated requirements/preferences go in [`REQUIREMENTS.md`](REQUIREMENTS.md) — reference this before making judgment calls.

## Related files
- [REQUIREMENTS.md](REQUIREMENTS.md) — standing requirements/preferences from the user
- [CURRENT-FEATURES.md](CURRENT-FEATURES.md) — feature tracker + history
- [figma-mcp-capabilities.md](figma-mcp-capabilities.md) — MCP capability audit
- [design-log.md](design-log.md) — design decision log
- [specs/](specs/) — one spec file per feature/task
