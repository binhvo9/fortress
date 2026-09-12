# Figma MCP Capability Audit (internal, unpolished)

Running notes on what the claude.ai Figma MCP connector can and can't do, discovered while building Fortress. Not meant for the public case study — reference only.

## Format
Each entry: what we tried → what happened → verdict (Works / Partial / Doesn't work / Workaround).

## Findings

### `whoami`
- Tried: check auth/plan.
- Result: returns handle, email, plan list (`Binh Vo's team`, tier `pro`, seat `Full`).
- Verdict: **Works.** Confirms Figma Pro is active and usable via MCP, and gives the `planKey` needed for `create_new_file`.

### `create_new_file`
- Tried: create a fresh design file named "Fortress — Wealth & Rental Portfolio App" in drafts.
- Result: succeeded, returned `file_key` + `file_url` instantly. **Gotcha (self-inflicted):** passed `&amp;` instead of `&` in the fileName string — it's a plain JSON string, not XML, so it got stored literally as `&amp;`. No rename tool exists in the exposed MCP surface (rename is a file-chrome action, not a Plugin API action) — fix has to be done by hand in the Figma UI.
- Verdict: **Works.** Minor gap: no MCP tool to rename a file after creation.

### `figma-use` skill (prerequisite for `use_figma`)
- Loaded the skill doc: it's a full Plugin API reference — auto-layout, variables, components/variants, text styles, effect styles, incremental-build workflow, a long list of gotchas (page-switching, font-loading recipe, layoutSizing rules, etc.), plus a `node.query()` CSS-selector-like API and `node.set()` batch updates.
- Verdict: this is not a thin wrapper — it's close to the real Figma Plugin API surface with strong guardrails baked into the instructions (incremental steps, mandatory ID returns, screenshot-to-verify loop). Complexity is real but well-scaffolded.

### `use_figma` — first write test
- Tried: create an auto-layout card (white fill, rounded corners, padding) with a heading + body TEXT node, using the canonical font-load-then-mutate recipe, then `card.screenshot()` inline.
- Result: **worked first try.** Correct auto-layout sizing, correct fonts/weights, inline screenshot rendered exactly as expected, returned created node IDs as instructed.
- Verdict: **Works well.** Inline `node.screenshot()` is a nice loop — no separate `get_screenshot` round-trip needed to sanity check a step.

### `get_metadata`
- Tried: read back page structure after the write test.
- Result: returns compact XML (ids, names, positions, sizes) — cheap way to verify structure without burning tokens on a screenshot.
- Verdict: **Works.** Good for structural validation; explicitly says to follow up with `get_design_context` if you need to *implement* the design (not needed for us — no code deliverable).

### `get_screenshot`
- Tried: screenshot the probe card by node ID.
- Result: returns a short-lived image URL + curl command (not inline base64 by default) — token-cheap. `node.screenshot()` inside `use_figma` is inline instead.
- Verdict: **Works.** Two screenshot paths exist: cheap URL (external tool) vs inline base64 (in-script verification). Pick based on whether you need to *see* it now or just want a link.

### `get_libraries` + `search_design_system`
- Tried: list libraries attached to a brand-new file, then search for "primary button" unscoped vs. scoped to a library key.
- Result: a **new file already comes with community libraries pre-attached** — Material 3 Design Kit, plus Apple's iOS/iPadOS, watchOS, visionOS, macOS kits (multiple versions). Unscoped search returned nothing; scoping to the Material 3 `libraryKey` returned full real component sets (Toggle button, Split button, Icon button, etc. — with variant/state descriptions) and real design tokens (M3 color scheme variables, opacity state-layer variables, fill styles).
- Verdict: **Works, and this is the big one.** MCP can pull entire production-grade component libraries (Material 3, Apple platform kits) straight into a file and reference their real components/variables/styles by key — meaning Fortress's design system doesn't have to be built from zero; it can start from Material 3 tokens/components and get customized on top. Gotcha: must scope `search_design_system` to a `libraryKey` (from `get_libraries`) or you get empty results.

### Variables + text styles (Spec 02)
- Tried: 15 primitive colors, 12 semantic color aliases (Light mode), spacing (7) + radius (4) scales, 8 text styles across two font families (Fraunces + Inter).
- Result: all worked. One real gotcha caught: `Fraunces` has no "Medium" style — available styles are Black/Bold/Italic/Light/Regular/SemiBold/Thin only. `listAvailableFontsAsync()` before assuming a weight exists is not optional, it's the only way to know — matches skill Rule 8/pre-flight checklist.
- Verdict: **Works.** Variable aliasing (`VARIABLE_ALIAS`), per-variable scopes, and WEB code syntax (`var(--x)`) all set correctly in one pass each.

### Transaction behavior on error (important!)
- Tried: a `use_figma` call that created a page + 9 components, then threw on `combineAsVariants` (wrong node type).
- Result: the **entire call's mutations were rolled back** — the page it created didn't exist on the next call either. Confirmed by listing pages afterward (only the original `Page 1` remained).
- Verdict: **Good to know, not documented explicitly in the skill.** Each `use_figma` invocation is atomic — a thrown error undoes everything that call did, not just the failing line. Don't assume partial progress survives a script error; if a multi-step script throws, plan to redo the whole call, not just patch the failing part.

### `combineAsVariants` requires COMPONENT nodes, not FRAME/auto-layout frames
- Tried: build variants with `figma.createAutoLayout()` (returns a FRAME) then `combineAsVariants`.
- Result: `Error: Cannot move node. A COMPONENT_SET node cannot have children of type other than COMPONENT`.
- Fix: use `figma.createComponent()` and set `layoutMode`/padding/sizing manually (components support the same auto-layout properties as frames) instead of `createAutoLayout`.
- Verdict: **Gotcha not obvious from the skill's "prefer createAutoLayout" guidance** — that guidance is FRAME-only; component variants need the manual path.

### Component-set frame doesn't auto-resize to fit repositioned variants
- Tried: after `combineAsVariants`, manually repositioned children into a 3×3 grid (setting x/y per child) expecting the parent to hug them.
- Result: the component-set frame kept its original size (from before repositioning) — height showed one row only even though children were positioned 3 rows deep. Screenshot looked cropped/wrong until caught.
- Fix: explicitly walk `buttonSet.children` after repositioning and `buttonSet.resize(maxX, maxY)`.
- Verdict: **Real gotcha.** Component sets don't auto-hug like auto-layout frames do — always resize manually after a manual grid layout, and validate with `get_metadata` (which shows real width/height) rather than trusting the screenshot alone.

### `figma.createVector()` bounding-box origin gotcha (Spec 03)
- Tried: draw a straight connector line between two points using `vectorPaths = [{ data: 'M 0 0 L dx dy' }]` then `node.x = x1; node.y = y1`.
- Result: for any line going right/down (positive dx/dy) this is correct. For a line going **left or up** (negative dx/dy), the line rendered shifted — because a vector's bounding box is computed from its path data (`minX`/`minY` of the path), not from local (0,0). When dx is negative, the path's own bbox starts at `dx`, not `0`, so setting `node.x = x1` actually places point `(dx,0)` at `x1`, not point `(0,0)` — shifting the visible line by `|dx|`. Visually this produced crossing/misaligned connector lines in a fan-out layout.
- Fix: `node.x = x1 + Math.min(0, dx)`, `node.y = y1 + Math.min(0, dy)` — compensates for the bbox origin so point `(0,0)` of the path lands exactly at `(x1,y1)` regardless of direction.
- Verdict: **Real gotcha, not covered in the skill docs.** Any time you draw a vector from relative path coordinates and then position it with `x`/`y`, remember Figma anchors the bounding box's top-left, not the path's first point — compensate whenever a path can go negative in either axis.

### `layoutSizingHorizontal/Vertical = 'FILL'` set inside a helper function, before the caller appends it (Spec 04)
- Tried: a `propertyCard(name, stats)` helper that created a Card instance and set `inst.layoutSizingHorizontal = 'FILL'` *inside* the function, before the caller appended the returned instance to its auto-layout parent.
- Result: `Error: FILL can only be set on children of auto-layout frames` — same root cause as skill Rule 12, but easy to reintroduce by accident once the FILL-setting is hidden inside a helper. Hit this twice in the same spec (once for stat tiles, once for property cards) before internalizing it.
- Verdict: **Not a new gotcha, but worth flagging: the skill's rule is easy to violate again once you factor node-creation into a helper function that both creates AND sets sizing.** Practical rule: helpers should create+configure but never set FILL — always set `layoutSizingHorizontal/Vertical = 'FILL'` in the caller, immediately after `appendChild`, never inside a factory function whose return value isn't parented yet.

### Component instances (`createInstance`, `findAll`/`findOne` on instance) — Spec 04
- Tried: instantiate Button and Card components with `mainComponent.createInstance()`, then edit nested text via `instance.findOne(n => n.type === 'TEXT')` / `findAll(...)`.
- Result: worked cleanly — text overrides apply per-instance without touching the source component, instance keeps all variable bindings (fill/radius/border) from the main component automatically.
- Verdict: **Works well.** This is the actual path for reusing Button/Card everywhere in screens instead of rebuilding them — much cheaper than rebuilding markup per screen.

### Text nodes default to `WIDTH_AND_HEIGHT` auto-resize (ignores container width) — Spec 06
- Tried: create a TEXT node with `figma.createText()` + `characters`, append into a fixed-width auto-layout container, expect it to wrap.
- Result: text overflowed the container edge instead of wrapping — because a freshly created text node's `textAutoResize` defaults to `WIDTH_AND_HEIGHT` (hug both axes, never wrap), not `HEIGHT`.
- Fix: explicitly set `textAutoResize = 'HEIGHT'` and `resize(fixedWidth, currentHeight)` on any text node that should wrap inside a container — matches the `figma-use` skill's pre-flight checklist item on this, but easy to skip for "just a label" text and only notice via screenshot.
- Verdict: **Confirms a documented gotcha is easy to actually hit.** Treat every multi-word text node as "wrapping text" by default and set width+HEIGHT explicitly, rather than special-casing only the ones that "look long."

### Paint-level `opacity` on `setBoundVariableForPaint` result doesn't visibly apply — use `node.opacity` instead (Spec 06)
- Tried: `area.fills = [{ ...boundPaint, opacity: 0.12 }]` to make an area-chart fill translucent.
- Result: rendered fully opaque — the spread + override didn't produce a visibly transparent fill (area looked like a solid dark shape instead of a soft wash).
- Fix: set opacity on the **node** instead — `area.opacity = 0.14` — which applied correctly.
- Verdict: **Real gotcha.** When a fill needs transparency on a variable-bound paint, prefer `node.opacity` over trying to merge `opacity` into the paint object returned by `setBoundVariableForPaint`. Didn't dig into why the paint-level override silently failed (variable binding may take precedence over the spread's extra key) — treat node-level opacity as the reliable path.

### Auto-layout frame's own sizing mode defaults to AUTO (hug), silently overriding a prior `resize()` (Spec 08)
- Tried: `figma.createFrame()` with `layoutMode = 'HORIZONTAL'`, then `resize(72, 72)` to make a fixed circular badge, without ever setting `primaryAxisSizingMode`/`counterAxisSizingMode`.
- Result: the frame rendered as a stretched pill, not a 72×72 circle — because an auto-layout frame's sizing mode defaults to `AUTO` (hug-content) on both axes, so the badge re-hugged its single text child's natural box (tall/narrow for a large glyph) and silently discarded the `resize(72,72)` call.
- Fix: explicitly set `primaryAxisSizingMode = 'FIXED'` and `counterAxisSizingMode = 'FIXED'` *before or with* `resize()` on any auto-layout frame that must stay a fixed size (icon badges, fixed-size containers) — don't rely on `resize()` alone once `layoutMode` is set.
- Verdict: **Real gotcha, adjacent to but distinct from the documented `layoutSizing*` rules** — this is about the *frame's own* axis sizing mode, not a child's `FILL`/`HUG`. Any auto-layout frame with a single glyph/short child is at risk of this if sizing mode isn't set explicitly.

### Prototype reactions (`setReactionsAsync`) — supported, but NAVIGATE requires same-page top-level frames (Spec 09)
- Tried: `instanceNode.setReactionsAsync([{ trigger: {type:'ON_CLICK'}, actions: [{type:'NODE', destinationId, navigation:'NAVIGATE', ...}] }])` to link a Dashboard button to a screen on a different page.
- Result: rejected with a clear error: *"for NAVIGATE actions, destinations must be a different top-level frame on the same page."* The API itself works (not "not implemented") — this is a real product constraint of Figma prototyping, not an MCP limitation, but it wasn't mentioned anywhere in the `figma-use` skill docs (no prototyping/reactions section exists in `api-reference.md` at all — this whole capability is undocumented in the skill).
- Consequence for this project: since Spec 04–08 put every screen on its own page (reasonable for organizing a design file), none of them can be wired to each other as-is. **Prototyping requires consolidating all screens onto one page first** — e.g., a dedicated "Prototype" page with copies or moved instances of every screen frame — before `setReactionsAsync` will accept a destination.
- Verdict: **Real, undocumented gotcha with real workflow impact.** Plan page structure with this constraint in mind from the start next time: either build all screens on one page from the outset, or budget a "consolidate to prototype page" step before wiring.

### Content bug, not an API bug: badge fill matched its background, making it invisible (Spec 13)
- Tried: a "Selected" checkmark badge bound to `color/bg/brand` sitting on top of an icon whose background was *also* `color/bg/brand` — only the badge's white stroke ring was visible, fill and checkmark both blended into the background.
- Not a Figma/MCP gotcha — a genuine design-logic mistake (reusing a semantic token without checking what's already behind it) that a screenshot caught immediately.
- Verdict: **Not a new API finding, but a good practical reminder for this whole workflow**: the screenshot-to-verify loop the `figma-use` skill recommends after each step catches exactly this kind of bug — code executes without error, structure is correct in `get_metadata`, and it's still wrong. Correctness checks can't stop at "no exception thrown."

### File sharing/permissions — not exposed by any available MCP tool
- Tried: looked for a way to set a file to "Anyone with the link can view" (public share) via the Figma MCP connector.
- Result: no tool in the exposed surface does this — `use_figma`'s Plugin API sandbox has no access to file-level sharing/permissions (that's a workspace-admin/REST concern, correctly walled off from a plugin running inside a file), and no dedicated MCP tool for it exists either.
- Verdict: **Real, confirmed gap.** Sharing settings must be changed by hand in the Figma UI (Share button → link access) — this is not something to keep retrying or work around programmatically.

## Summary so far
- Auth, file creation, node creation/editing, structural + visual read-back, and community design-system import all **work** and work well.
- Biggest capability surprise: pre-attached community libraries + working `search_design_system` — this changes the plan for Spec 02 (design system) from "build from scratch" to "start from Material 3 tokens, restyle to Fortress brand."
- Only gap found so far: no MCP tool to rename a file after `create_new_file` (cosmetic, fix by hand in Figma UI).
