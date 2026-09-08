---
name: saas-sidebar-redesign
description: Redesign the Vue 3 client into a modern SaaS-style interface with a left vertical sidebar nav (replacing the top nav bar), a consistent spacing/color token system, and a polished professional look. Use this skill when asked to redesign, restyle, or modernize the UI, move the top nav to a sidebar, or unify spacing/visual style across views.
---

# SaaS Sidebar Redesign

Converts the app shell in `client/src/App.vue` from a top-nav layout to a
fixed left sidebar, and re-tokens the ad hoc styling in `App.vue` (and the
views/components that reuse its classes) into a small, consistent design
system. This is a visual/layout change only — routes, component logic, API
calls, and i18n keys stay the same.

## Before touching any `.vue` file

Per the root `CLAUDE.md`: **any creation or significant modification of a
`.vue` file must be delegated to the `vue-expert` subagent.** This skill is
the spec to hand that subagent — do the edits through it, not directly.
Work in batches (e.g. "App.vue shell" as one dispatch, then views in groups
of 2-3) so each batch can be visually checked before moving on.

## Current state (what you're replacing)

- `App.vue` renders `.top-nav` (70px sticky header, logo + horizontal
  `.nav-tabs`) and owns the **global** `<style>` block — every view and
  component reuses its classes (`.card`, `.stat-card`, `.badge`, `table`,
  etc.) rather than scoping their own.
- `FilterBar.vue` is a second sticky bar pinned at `top: 70px` (hardcoded to
  the nav's height) directly under `.top-nav`.
- Colors and spacing are hardcoded hex/rem literals scattered through
  `App.vue`'s stylesheet (e.g. `#0f172a`, `#64748b`, `#e2e8f0`, `0.625rem`,
  `0.938rem`) — the same handful of values retyped slightly differently in
  many places, not shared tokens.
- Nav items today: Overview (`/`), Inventory, Orders, Finance (`/spending`),
  Demand Forecast (`/demand`), Reports — labels come from `t('nav.*')` in
  `client/src/locales/en.js` / `ja.js`. Keep using those keys; don't hardcode
  new label strings.

## Target layout

```
┌──────────┬─────────────────────────────────────────┐
│          │  page title · breadcrumb      [profile]  │  ← slim top bar (56px)
│ Sidebar  ├─────────────────────────────────────────┤
│ 260px    │  FilterBar (4 filters, inline in content) │
│ fixed    ├─────────────────────────────────────────┤
│ full     │                                           │
│ height   │  router-view content                     │
│          │                                           │
└──────────┴─────────────────────────────────────────┘
```

- Sidebar: `position: fixed; left:0; top:0; bottom:0; width:260px`, its own
  background/border, **not** part of the scrolling flow.
- Sidebar top: logo + company name (from `t('nav.companyName')` /
  `t('nav.subtitle')`). Sidebar body: vertical nav list, one item per route.
  Sidebar bottom: `ProfileMenu` (moves here from the old top-nav).
- `LanguageSwitcher` moves into the slim top bar (top-right), since it isn't
  a navigation item.
- Main content wrapper gets `margin-left: 260px` and keeps its own
  `max-width` + padding for the content column.
- `FilterBar` stops using a hardcoded `top: 70px` sticky offset — it either
  becomes a non-sticky bar inside the content column, or sticks to `top: 0`
  relative to the (now un-nested) content scroll area. Check both after the
  change; don't leave a stale magic-number offset.
- Below a `1024px` breakpoint, collapse the sidebar to an off-canvas panel
  toggled by a hamburger button in the top bar — don't just shrink it in
  place, it'll clip the nav labels.

## Design tokens

Replace the scattered literals with CSS custom properties declared once on
`:root` in `App.vue`'s global stylesheet. Derive values from what's already
in use (don't invent an unrelated palette) and give the muted/border tones a
slight slate bias rather than flattening them to pure gray:

```css
:root {
  /* surface */
  --bg: #f8fafc;
  --surface: #ffffff;
  --surface-sunken: #f1f5f9;
  --border: #e2e8f0;

  /* text */
  --ink: #0f172a;
  --muted: #64748b;

  /* brand */
  --accent: #2563eb;
  --accent-soft: #eff6ff;

  /* status — keep semantics used by .badge.* and .stat-card.* today */
  --success: #059669; --success-soft: #d1fae5; --success-ink: #065f46;
  --warning: #ea580c; --warning-soft: #fed7aa; --warning-ink: #92400e;
  --danger:  #dc2626; --danger-soft:  #fecaca; --danger-ink:  #991b1b;
  --info:    #2563eb; --info-soft:    #dbeafe; --info-ink:    #1e40af;

  /* spacing scale — 4px base, replaces one-off rem values */
  --space-1: 0.25rem;  /* 4px  */
  --space-2: 0.5rem;   /* 8px  */
  --space-3: 0.75rem;  /* 12px */
  --space-4: 1rem;     /* 16px */
  --space-5: 1.25rem;  /* 20px */
  --space-6: 1.5rem;   /* 24px */
  --space-8: 2rem;     /* 32px */

  /* radius + elevation — reuse, don't add a third radius value */
  --radius: 10px;
  --radius-sm: 6px;
  --shadow-sm: 0 1px 3px 0 rgba(15, 23, 42, 0.05);
  --shadow-md: 0 4px 12px rgba(15, 23, 42, 0.08);

  --sidebar-width: 260px;
  --topbar-height: 56px;
}
```

Then update `.card`, `.stat-card`, `.badge.*`, `table`/`th`/`td`, `.loading`,
`.error`, and the page-header rules to read from these variables instead of
their inline hex/rem values. This is a mechanical pass — don't change the
class names or component markup that already reference them, or every view
breaks.

## Nav icons

The project's design system explicitly forbids emoji in the UI (see root
`CLAUDE.md`). Each sidebar nav item needs a small icon next to its label —
use hand-authored inline `<svg>` (24×24, single stroke color inheriting
`currentColor`, ~1.5–1.75 stroke width), one per route, not an emoji and not
a new icon-font/library dependency. Keep the stroke weight and viewBox
consistent across all six so the row heights line up.

## Active state

Swap the old underline (`.nav-tabs a.active::after`) for a treatment that
reads correctly in a vertical list: a left accent bar (`3px`, `--accent`)
plus a soft background (`--accent-soft`) on the active row, icon and label
colored `--accent`. Inactive rows use `--muted`, hover uses
`--surface-sunken`.

## Verification

1. Start the app (`/start` skill, or `cd server && uv run python main.py` +
   `cd client && npm run dev`).
2. Use the Chrome/Playwright MCP tools to visit all six routes plus
   `/reports`, confirming: sidebar stays fixed while content scrolls, the
   active nav item highlights correctly on each route, `FilterBar` no longer
   has a stale sticky offset, `ProfileMenu`/`TasksModal`/`ProfileDetailsModal`
   still open correctly from their new sidebar location, and the language
   switcher still works from the top bar.
3. Resize below 1024px and confirm the sidebar collapses to the off-canvas
   pattern instead of clipping.
4. Spot-check both light and dark-heavy data states (e.g. a view with an
   empty/loading state) since `.loading`/`.error` also moved onto tokens.
5. Don't add new npm dependencies for this — `client/.npmrc` pins the public
   registry deliberately (see root `CLAUDE.md`); everything above is
   achievable with plain CSS and inline SVG.
