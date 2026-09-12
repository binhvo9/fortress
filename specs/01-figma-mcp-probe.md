# Spec 01: Figma MCP Capability Probe

## Goal
Find out what the claude.ai Figma MCP connector can actually do before relying on it for real design work. Avoid discovering hard limits mid-build.

## Scope
In:
- Check `whoami` / connection, list libraries/files available.
- Try creating a new Figma file via MCP.
- Try generating a simple design (e.g. one screen) via `use_figma` / generative flow.
- Try reading design context back (`get_design_context`, `get_metadata`, `get_screenshot`).
- Note auth/permission prompts, speed, fidelity of output, any silent failures.

Out:
- Not building any real Fortress screens yet.
- Not evaluating Code Connect / dev handoff (later spec, only if relevant).

## Approach
1. Load the `/figma-use` skill/resource first (required before `use_figma`).
2. Run through the checklist above against a scratch Figma file.
3. Log every finding (works / partial / broken / workaround) into `figma-mcp-capabilities.md` as we go.

## Done when
- `figma-mcp-capabilities.md` has at least one entry per checklist item above.
- We know whether MCP can generate a usable first screen end-to-end, or whether manual Figma work is required alongside it.
