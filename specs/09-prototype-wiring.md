# Spec 09: Prototype Wiring

## Goal
Wire the 7 built screens into a clickable prototype so the case study can link a real "Try it" prototype, not just static screens.

## Scope
In:
- Consolidate all 7 screen frames onto one "Prototype" page (required — `setReactionsAsync` NAVIGATE only works between top-level frames on the same page; discovered during this spec, see `figma-mcp-capabilities.md`).
- Wire the primary happy-path flows:
  - Dashboard → Properties tab → Properties List → tap property card → Property Detail → Back → Properties List
  - Dashboard → Reports tab → Cashflow & Reports
  - Dashboard → Add Property (quick action) → Add Property Step 1 → Continue → Success → Go to Property → Property Detail
  - Tab bar Home taps (from Properties List / Cashflow Reports) → back to Dashboard
- Set Dashboard as the flow starting point.

Out:
- Wiring every single tab combination exhaustively (e.g., Profile tab, Net Worth Timeline entry point) — cover the primary path only, note gaps.

## Approach
1. Create "Prototype" page, move all 7 screen frames onto it, arrange in a grid.
2. Set flow starting point at Dashboard.
3. Wire reactions per the happy-path list above.
4. Verify by inspecting `reactions` on each wired node.

## Done when
- All 7 screens live on one page.
- Flow starting point set.
- Happy-path reactions wired and verified.
