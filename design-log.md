# Design Decision Log

Raw material for the eventual case study. Append entries as decisions get made — what was decided, why, what was rejected.

## Brand direction — 2026-09-12

Chose **forest green (primary/brand) + gold (accent) + cream/charcoal (neutrals)** instead of generic fintech blue or Donor Hub-style red. Rationale: green reads as growth/money/stability, gold reads as premium/value, and the combo fits "financial fortress" better than a SaaS-blue dashboard would. Deliberately avoided colors already used in the Donor Hub case study reviewed earlier, to keep the portfolio visually distinct across pieces.

Type pairing: **Fraunces** (serif, for Display/H1/H2 — gives a premium, editorial feel to headlines) + **Inter** (for H3/body/UI — reliable, high-legibility workhorse). Verified via `listAvailableFontsAsync` before committing (Fraunces has no "Medium" weight — used Regular for H2 instead).

## Design system foundation — 2026-09-12

Built: Primitives (15 raw colors) → semantic Color collection (15 aliases, Light mode) → Spacing scale (7 steps, 4–64px) → Radius scale (4 steps) → 8 text styles → 3 components (Button: 9 variants across Style×State, Input: 4 states, Card: base container).

Card component's placeholder content ("Mirpur Boarding House, 6 rooms · 5 occupied · ROI 14.2%/yr") already previews the actual Fortress product surface — property tile with occupancy + ROI at a glance. Good sign the concept translates visually.

## Information architecture — 2026-09-12

Screen list, top-down:

- **Splash** → **Onboarding** (3-slide value prop) → **Login / Sign Up**
- **Dashboard (Home)** — net worth summary, cashflow snapshot, quick actions. Root after login.
  - **Properties** (list of all properties/phòng trọ)
    - **Property Detail** (rooms, tenants, occupancy, per-property cashflow)
      - **Add / Edit Property**
      - **Room / Tenant Detail** (rent status, contact, lease dates)
  - **Add Property** (quick-action flow, reachable from Dashboard directly too)
  - **Cashflow & Reports** (income vs. expenses, ROI by property, trends)
  - **Net Worth / Freedom Timeline** (assets vs. liabilities over time, projected date to financial freedom)
  - **Notifications** (rent due, lease expiring, AI insights/alerts)
  - **Profile & Settings**

Kept flat and shallow on purpose — max depth 3 from Dashboard (Dashboard → Properties → Property Detail → Add/Edit or Room Detail). Matches a portfolio-scope app: enough to be a real IA, not a bloated enterprise tree.
