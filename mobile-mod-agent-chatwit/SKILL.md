---
name: mobile-mod-agent-chatwit
description: Implement, extend, review, or fix the Chatwit mobile module under components-next/mobile/ with strict desktop isolation. Use this skill whenever the user asks for Chatwit mobile mode, PWA/mobile UI, native Chatwoot mobile parity, swipe gestures, mobile conversation details/actions, mobile settings, push notifications, or any screen/layout/interaction that should exist only on small screens, even if they do not explicitly mention the mobile folder.
category: chatwit
source: witdev
date_added: "2026-03-14"
---

# Mobile Mod Agent Chatwit

This skill is for mobile work in the Chatwit fork only. The mobile module is a dedicated visual layer for small screens and must stay isolated from desktop behavior.

## First read

Before proposing or editing code, read these project docs if they exist in the workspace:

1. `chatwitdocs/Chatwoot-Chatwit-mobile.md`
2. `chatwitdocs/mobile-fixes.md`
3. `chatwitdocs/chatwoot-mobile-app/` as a layout and interaction reference only

If the paths are missing, search the repo for equivalent files before proceeding.

For a condensed reference, read `references/chatwit-mobile-architecture.md` bundled with this skill.

## Core rules

### 1. Isolate mobile from desktop

The mobile module is a conditional visual shell. Treat desktop as untouchable unless a tiny compatibility guard is absolutely necessary.

- Prefer edits in `app/javascript/dashboard/components-next/mobile/`
- Allow small conditional rendering guards in `Dashboard.vue` only when needed to keep mobile isolated
- Do not rewrite shared desktop flows just to make mobile easier
- Do not regress desktop routing, command bar, sidebar, conversation views, or composer behavior

### 2. Connect, do not recreate

Every mobile feature should reuse logic that already exists in desktop code.

- Reuse Vuex stores, actions, getters, composables, API clients, and existing request contracts
- Search desktop conversation code before writing new logic
- Reuse patterns like `useMapGetter`, `store.dispatch`, existing selectors, and existing bottom sheets/dialogs when appropriate
- Never create a second business-logic path for status, labels, assignee, team, priority, participants, push, or contact data

### 3. Native parity is visual, not architectural

The React Native app in `chatwitdocs/chatwoot-mobile-app/` is a reference for:

- layout structure
- spacing and hierarchy
- gesture direction
- card grouping
- action order
- section naming

It is not a source of business logic. Do not port React Native logic into Chatwit if the same behavior already exists in Vue/desktop stores.

### 4. Respect Chatwit mobile constraints

- Mobile code lives in `app/javascript/dashboard/components-next/mobile/`
- Mobile translations live in `app/javascript/dashboard/i18n/locale/*/mobile.json`
- Styling is Tailwind only
- Push is Web Push via VAPID and `public/sw.js`, never Firebase/FCM
- The desktop version must remain behaviorally identical after your change

## Required workflow

Follow this sequence for every mobile task.

### Step 1. Rebuild context

Read the docs above, then inspect the target mobile component and its desktop counterpart.

For conversation work, usually inspect:

- `app/javascript/dashboard/components-next/mobile/`
- `app/javascript/dashboard/routes/dashboard/conversation/`
- `app/javascript/dashboard/components/widgets/conversation/`
- `app/javascript/dashboard/store/modules/`

For app-shell work, usually inspect:

- `app/javascript/dashboard/routes/dashboard/Dashboard.vue`
- `app/javascript/dashboard/components-next/mobile/MobileLayout.vue`

### Step 2. Find the reusable desktop logic

Search for the exact data flow before writing code.

Common reuse targets:

- conversation status: `toggleStatus`
- assignee/team: `assignAgent`, `assignTeam`, `setCurrentChatAssignee`, `setCurrentChatTeam`
- priority: `assignPriority`, `setCurrentChatPriority`
- labels: `conversationLabels/get`, `conversationLabels/update`
- participants/watchers: `conversationWatchers/show`, `conversationWatchers/update`
- push notifications: existing push helpers, service worker, and notification subscription flows

If you cannot point to the existing source of truth, stop and keep searching before implementing.

### Step 3. Build the mobile-only presentation layer

Implement the UX inside mobile components. Prefer:

- new files under `components-next/mobile/`
- mobile-only wrappers around existing behavior
- gesture handling that does not conflict with route navigation gestures
- native-feeling transitions, spacing, and hierarchy

When creating new mobile UI, keep the intent explicit:

- first page: primary conversation or list surface
- second page or sheet: actions/details/settings
- drawers/sheets: selection and editing helpers

### Step 4. Validate isolation

Always verify:

- no desktop-only files were changed unnecessarily
- no shared component was modified in a way that changes desktop behavior unless explicitly intended
- mobile warnings and accessibility regressions were not introduced
- new strings exist in `en`, `pt`, and `pt_BR` mobile locale bundles when needed

### Step 5. Document the fix

Update the mobile docs when the change is meaningful:

- `chatwitdocs/mobile-fixes.md`
- `chatwitdocs/Chatwoot-Chatwit-mobile.md`

Document what was added, what desktop logic was reused, and how mobile isolation was preserved.

## Design heuristics

Use the native app reference to improve fidelity without breaking web ergonomics.

- Favor strong section grouping over long undifferentiated lists
- Keep headers compact and app-like
- Use cards, sheets, and paged surfaces for task separation
- Use gesture hints sparingly and only when they aid discovery
- Give transitions depth and snap feedback when the interaction is gesture-driven

## Vue implementation guidance

- Prefer Vue 3 Composition API
- Keep state local when it is purely visual
- Push persistent state changes through existing store actions
- Avoid broad watchers that can re-trigger desktop flows
- Use computed values for derived display data instead of duplicating store state

## Do not do these things

- Do not reimplement desktop business logic in mobile components
- Do not add third-party mobile SDKs for solved features
- Do not move mobile behavior into unrelated desktop folders unless there is a hard technical reason
- Do not change desktop UX while working on mobile parity
- Do not treat the React Native reference app as permission to invent missing backend behavior

## Completion checklist

Before finishing, confirm all of the following:

- The request is implemented in mobile mode
- Desktop behavior is preserved
- Existing stores/actions/composables were reused
- Required i18n keys were added to mobile locale bundles
- Mobile docs were updated when the change was substantial
- The changed files were validated for editor errors

## Trigger examples

Use this skill for prompts like:

- "deixa o mobile idêntico ao app do chatwoot"
- "no modo mobile, ao abrir conversa e arrastar para a esquerda tem que abrir a tela 2"
- "corrige esse bug só no mobile, sem tocar no desktop"
- "conecta labels e participantes na tela mobile de detalhes"
- "faz o settings mobile usar o fluxo real de push web"

Do not use this skill for:

- upstream merge requests unrelated to mobile
- generic desktop conversation/sidebar changes
- React Native app development outside the Chatwit web mobile layer