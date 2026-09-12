# Spec 08: Screen — Add Property flow

## Goal
Design the "Add Property" flow reachable from Dashboard/Properties quick actions — the main data-entry flow in the app.

## Scope
In:
- Step 1: Property Details form (name, address, number of rooms, monthly rent target) using Input component instances + a step progress indicator.
- Step 2: Confirmation / success screen (property created, CTA back to Properties).

Out:
- Room-by-room setup screen (can be inferred, not required for portfolio scope).
- Real form validation states beyond what Input component already covers.

## Approach
1. Build Step 1 form screen: header with close/cancel, step indicator (1 of 2), stacked Input fields with labels, primary CTA button.
2. Build Step 2 success screen: centered confirmation state, primary CTA.
3. Screenshot both, validate.

## Done when
- Both screens render correctly using Input/Button components and design tokens.
