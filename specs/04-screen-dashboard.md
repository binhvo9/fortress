# Spec 04: Screen — Dashboard (Home)

## Goal
Design the mobile Dashboard/Home screen — the root screen after login, and the most important screen for the case study cover.

## Scope
In:
- iPhone-size frame (390×844).
- Net worth summary (hero number + trend).
- Cashflow snapshot (income vs expenses this month).
- Quick actions (Add Property, Record Payment, View Reports — using Button component).
- Properties preview list (using Card component, 2-3 items with occupancy/ROI).
- Bottom tab bar (Home, Properties, Reports, Profile).

Out:
- Other screens (separate specs).
- Real interaction/prototype links (later spec).

## Approach
1. Build screen frame + status bar placeholder.
2. Build sections top to bottom, reusing Button/Input/Card components and color/type tokens from the design system — don't hardcode values.
3. Validate with screenshot after each major section.

## Done when
- Full Dashboard screen renders correctly in one screenshot, using only design-system tokens/components (no hardcoded hex/px where a token exists).
