# Spec 06: Screen — Net Worth / Freedom Timeline

## Goal
Design the most emotionally resonant screen in Fortress: net worth trajectory over time and projected "financial freedom" date. This is the screen most likely to anchor the case study cover/hero shot.

## Scope
In:
- Back header.
- Net worth trend chart (assets vs. liabilities or net worth line over time) — simple, legible, not a generic line chart; follow dataviz principles (clear axis, restrained color, one clear takeaway).
- Freedom milestone callout: target net worth, projected date, progress %.
- Asset breakdown (property equity vs. cash/other, simple stacked bar or list).
- Tab bar (Reports or Home active — decide based on IA; likely reachable from Reports tab).

Out:
- Editable goal-setting UI (future scope if ever needed).
- Interactive chart states.

## Approach
1. Build header + freedom milestone hero callout first (text-heavy, establishes the narrative).
2. Build the trend chart using simple SVG-like vector paths or a bar/line built from rectangles — kept simple and legible over clever.
3. Build asset breakdown section.
4. Screenshot, validate legibility (contrast, no overlapping labels).

## Done when
- Screen renders correctly, chart is legible and not overlapping, uses design tokens, NZ-appropriate content (dollar amounts fine as-is, no location names needed here).
