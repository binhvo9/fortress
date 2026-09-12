# Spec 07: Screen — Cashflow & Reports

## Goal
Design the Reports screen: income vs. expenses over time, and ROI comparison across properties. This is the "prove it with numbers" screen, complementary to the more emotional Net Worth Timeline.

## Scope
In:
- Header + month selector (simple, e.g. "September 2026" with prev/next affordance).
- Income vs Expenses bar chart (monthly, a few months of history).
- ROI by property — ranked list/bars comparing the 4 properties from Spec 05.
- Tab bar with Reports active.

Out:
- Exportable/downloadable reports.
- Filtering UI beyond the month selector.

## Approach
1. Header + month selector.
2. Income/Expenses grouped bar chart (hand-built vectors/rectangles, consistent with Net Worth Timeline's chart approach).
3. ROI-by-property ranked bars, reusing property names from Spec 05.
4. Screenshot, validate legibility.

## Done when
- Screen renders correctly, charts legible, reuses design tokens and the same NZ property names already established.
