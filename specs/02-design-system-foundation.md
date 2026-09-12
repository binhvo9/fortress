# Spec 02: Design System Foundation

## Goal
Establish Fortress's visual foundation in Figma: brand color variables, type scale, spacing scale, and 2-3 core components (button, input, card) — production-quality, not placeholder shapes.

## Scope
In:
- Brand direction: pick a palette/mood that fits "financial fortress" (trustworthy, grounded, a little premium — not generic SaaS blue, not the Donor Hub red).
- Variable collections: color (primitives + semantic aliases, light mode first), spacing, corner radius.
- Type scale using a real font (verify via `listAvailableFontsAsync`).
- 2-3 foundational components as proper variant sets: Button (primary/secondary/ghost × states), Input field, Card container.
- Leverage Material 3 library already attached to the file (per `figma-mcp-capabilities.md`) as a structural reference where useful — but Fortress gets its own tokens/brand, not raw M3 styling.

Out:
- Full screens (next spec).
- Dark mode (defer unless trivial to add alongside).
- Every component in existence — just enough to build the first screens.

## Approach
1. Load `figma-generate-library` + `figma-use` skills together (required for component/variable creation).
2. Decide brand direction (palette, type) — log the decision + rationale in `design-log.md`.
3. Build variable collections first (colors → spacing → radius), validate with `get_metadata`.
4. Build components on top of variables (bind, don't hardcode), validate with screenshots.
5. Log every new MCP finding into `figma-mcp-capabilities.md` as it comes up.

## Done when
- A "Foundations" page (or section) in the Figma file has color/spacing/radius variables and a type scale.
- Button, Input, Card exist as proper component sets with variables bound (not hardcoded hex/px).
- Screenshot confirms visual correctness.
- `design-log.md` has an entry explaining the brand direction chosen.
