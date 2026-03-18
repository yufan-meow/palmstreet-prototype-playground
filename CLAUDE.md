# Palmstreet Prototype Playground — Claude Context

This is a shared prototyping environment for the Palmstreet design team.
Read this file before writing any code.

---

## What This Repo Is

A Nuxt 3 app where designers build interactive prototypes using Palmstreet's
Branch Design System tokens. Every branch auto-deploys to a Vercel preview URL.
Engineers review prototypes by opening that URL — no local setup needed.

---

## Stack

- **Framework:** Nuxt 3
- **Styling:** CSS custom properties (semantic tokens from Branch DS)
- **Typeface:** Gabarito only — loaded via Google Fonts in `nuxt.config.ts`
- **Icons:** Google Material Design Icons, filled + rounded variant only
- **No component library** — build from scratch using tokens below

---

## File Structure Rules

Each designer gets their own folder under `/pages/`:

```
/pages
  /yufan/              ← Yufan's prototypes
    my-prototype.vue   ← becomes /yufan/my-prototype
  /[designer-name]/    ← other designers
  /shared/             ← shared example components and utilities
/components/           ← reusable prototype components
/styles/
  tokens.css           ← ALL Branch DS semantic tokens (source of truth)
  base.css             ← reset, Gabarito font, body defaults
```

**Never put prototype files directly in `/pages/` root.** Always inside a designer subfolder.

---

## Design System Tokens

All tokens live in `/styles/tokens.css`. Always use `var(--token-name)`.
Never use raw hex values. Never reference base/global token names.

### Content Tokens (text, icons — use with `color`, `fill`, `stroke`)
```
--content-primary         Primary text and icons
--content-secondary       Supporting text
--content-tertiary        Muted text, placeholders
--content-quaternary      Disabled states
--content-primary-inverse Primary text on dark/filled surfaces
--content-always-white    Always white, never flips
--content-always-black    Always black, never flips
--content-interactive     Links, active tabs, selected states
--content-destructive     Error text and icons
--content-success         Success text and icons
--content-warning         Warning text and icons
```

### Section Tokens (surfaces — use with `background-color`)
```
--section-basement        Base page background
--section-floor-1         Cards, panels, containers
--section-floor-2         Popovers, dropdowns, floating UI
--section-basement-inverse Inverted base background
--section-floor-1-inverse  Inverted card surface (used by Toast)
--section-interactive     Hover and focus surfaces
--section-destructive     Error backgrounds
--section-success         Success backgrounds
--section-warning         Warning backgrounds
--section-cta             #B9EC51 — primary CTA only, never decorative
--section-always-white    Always white surface
--section-always-black    Always black surface
--scrim-heavy             Modal overlays
--scrim-light             Drawer overlays
```

### Line Tokens (borders — use with `border-color`)
```
--border                  Default borders and dividers
--border-strong           Focused inputs, emphasized borders
--border-destructive      Error input borders
--border-success          Success input borders
```

---

## Typography

Only use Gabarito. Never use system fonts.

| Token name       | Size | Weight | Use |
|------------------|------|--------|-----|
| Display0         | 70px | 500    | Hero |
| Display1         | 57px | 600    | Large display |
| Headline1        | 32px | 400    | Page headlines |
| Headline2        | 28px | 400    | Section headlines |
| Headline3        | 24px | 600    | Sub-headlines |
| Title1           | 22px | 500    | Titles |
| Title2           | 18px | 600    | Page titles (mobile nav) |
| Title3           | 16px | 500    | Section titles |
| Body1            | 16px | 400    | Primary body text |
| Body2            | 14px | 400    | Secondary body text |
| Label1           | 12px | 600    | Input labels, tags |
| Label2           | 10px | 600    | Small labels |
| Button1          | 16px | 500    | Button text (medium) |
| Button2          | 14px | 500    | Button text (small) |

---

## Spacing

8px grid. Use only: `0, 2, 4, 8, 12, 16, 24, 32, 40, 48`. Use `9999px` for pill shapes.

---

## Platform Rules

**Desktop:** sidebar nav + main content, no fixed width
**Mobile:** 375px base, full-width stacked layout
- `Docked Button` (375×98px) — mobile-only fixed bottom action bar
- `Page Title` (375×92px) — mobile-only top nav bar with back arrow

---

## Component Rules

### Button
- Primary: `background: var(--section-cta)`, label: `var(--content-always-black)` — ALWAYS always-black, never content-primary
- Secondary: `background: var(--section-floor-1)`, border: `var(--border)`, label: `var(--content-primary)`
- Destructive: `background: var(--section-destructive)`, label: `var(--content-always-white)`
- Height: 40px (medium), 32px (small)

### Toast
- ALWAYS uses inverse tokens regardless of theme
- Background: `var(--section-floor-1-inverse)`, text: `var(--content-primary-inverse)`

### Icons
- Google Material Icons, filled + rounded ONLY
- Color always set via a Content token, never hardcoded

---

## Light / Dark Mode

Dark mode is handled entirely via CSS variable swapping at `:root[data-theme="dark"]`.
No component-level overrides needed. The same token names work in both modes.

---

## Rules for Code Generation

1. Never use raw hex or base token names — always `var(--token-name)`
2. Never use any typeface other than Gabarito
3. Always implement both light and dark mode via the token layer
4. Follow the 8px spacing grid
5. Primary button label is always `--content-always-black`
6. Toast always uses inverse tokens
7. Docked Button and Page Title are mobile-only
8. Icons: Google Material Design, filled, rounded only
9. Icon color is always a Content token
10. No generic SaaS aesthetics — neutral, marketplace character

---

## Adding a New Prototype

1. Create `/pages/[your-name]/[prototype-name].vue`
2. Use tokens from `/styles/tokens.css`
3. Keep it self-contained — inline styles are fine for prototypes
4. Push your branch — Vercel auto-generates a preview URL
5. Share the URL in Slack or Linear for engineer review
