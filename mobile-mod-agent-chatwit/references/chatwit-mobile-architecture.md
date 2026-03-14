# Chatwit Mobile Architecture Reference

Use this reference after reading the repo docs. It summarizes the non-negotiable architecture for Chatwit mobile work.

## Mission

Build and maintain the Chatwit mobile web module so it behaves like a native app on small screens while remaining a thin visual layer over existing Chatwit desktop logic.

## Non-negotiable rules

### Isolate mobile

- Mobile is conditional UI for `width < 768px`
- Desktop must not regress
- Prefer mobile-only files under `app/javascript/dashboard/components-next/mobile/`

### Connect existing logic

- Reuse Vuex modules and existing actions
- Reuse existing conversation request contracts
- Reuse existing push flow with Web Push + VAPID
- Reuse desktop data sources for assignee, team, labels, priority, participants, contact details, and conversation status

### Use native app only as a visual guide

Read `chatwitdocs/chatwoot-mobile-app/` to understand:

- screen composition
- swipe/pager behavior
- action ordering
- section hierarchy
- visual rhythm

Do not port React Native architecture or business logic.

## Important project paths

### Docs

- `chatwitdocs/Chatwoot-Chatwit-mobile.md`
- `chatwitdocs/mobile-fixes.md`

### Mobile implementation

- `app/javascript/dashboard/components-next/mobile/`

### Common desktop logic sources

- `app/javascript/dashboard/routes/dashboard/conversation/`
- `app/javascript/dashboard/components/widgets/conversation/`
- `app/javascript/dashboard/store/modules/`

### Entry points

- `app/javascript/dashboard/routes/dashboard/Dashboard.vue`
- `app/javascript/dashboard/components-next/mobile/MobileLayout.vue`

## Common mobile patterns already used in Chatwit

- bottom tab navigation for inbox, conversations, settings
- mobile-only sheets for filters and actions
- route-safe swipe back from the left edge
- in-conversation swipe pager for chat/details surfaces
- haptic feedback for gesture thresholds where supported

## Validation mindset

Always check:

- editor errors in changed files
- mobile locale coverage in `en`, `pt`, `pt_BR`
- no desktop logic regressions caused by shared component edits
- docs updated for substantial fixes or new mobile capabilities