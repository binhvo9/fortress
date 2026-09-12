# Spec 05: Screens — Properties List + Property Detail

## Goal
Design the two screens for the "Properties" branch of the IA: the full list and the drill-down detail.

## Scope
In:
- **Properties List** screen (390×844): header, full list of property Cards (4), tab bar with Properties active, floating/quick add action.
- **Property Detail** screen (390×844): back header with property name, stat row (occupancy, ROI, monthly rent), room/tenant list (room number, tenant name, rent-status chip), edit action.
- All placeholder content uses NZ suburb names (per `REQUIREMENTS.md`).

Out:
- Add/Edit Property form (separate future screen if needed).
- Room/Tenant Detail screen (separate future screen if needed).

## Approach
1. Build Properties List screen, reusing Card/Button instances + tokens.
2. Build Property Detail screen with a new "stat row" pattern and a room-list-item pattern (new small pieces, bind to tokens, no new component-set needed unless reused 3+ times).
3. Screenshot both, validate.

## Done when
- Both screens render correctly, use design-system tokens/components, and use NZ placeholder content.
