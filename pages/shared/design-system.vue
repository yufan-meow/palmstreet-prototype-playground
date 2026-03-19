<template>
  <div class="ds-root">

    <!-- Proto bar -->
    <div class="proto-bar">
      <NuxtLink to="/" class="back-link">
        <span class="material-symbols-rounded" style="font-size:20px">arrow_back</span>
        <span>Playground</span>
      </NuxtLink>
      <span class="proto-label">Branch Design System</span>
      <button class="proto-toggle" @click="toggleTheme()">
        <span class="material-symbols-rounded" style="font-size:18px">{{ theme === 'dark' ? 'light_mode' : 'dark_mode' }}</span>
      </button>
    </div>

    <div class="ds-layout">

      <!-- ── Sidebar nav ── -->
      <nav class="ds-nav">
        <a v-for="s in sections" :key="s.id" :href="'#' + s.id" class="nav-link" :class="{ active: activeSection === s.id }">
          {{ s.label }}
        </a>
      </nav>

      <!-- ── Main content ── -->
      <main class="ds-main">

        <!-- HERO -->
        <div class="ds-hero">
          <p class="ds-eyebrow">Palmstreet</p>
          <h1 class="ds-headline">Branch Design System</h1>
          <p class="ds-sub">The single source of truth for all UI decisions in the Seller Hub. Use tokens from <code>styles/tokens.css</code>. Never use raw hex values in components.</p>
          <div class="ds-meta-row">
            <span class="ds-chip">Nuxt 3</span>
            <span class="ds-chip">CSS Custom Properties</span>
            <span class="ds-chip">Gabarito</span>
            <span class="ds-chip">Material Icons · Filled · Rounded</span>
          </div>
        </div>

        <!-- ══════════════════════════════════════════
             RULES
        ══════════════════════════════════════════ -->
        <section :id="sections[0].id" class="ds-section">
          <h2 class="ds-section-title">Rules for Code Generation</h2>
          <p class="ds-section-desc">These rules apply to all code written for the Seller Hub — human or AI generated.</p>

          <div class="rules-list">
            <div v-for="(rule, i) in rules" :key="i" class="rule-item">
              <span class="rule-num">{{ i + 1 }}</span>
              <span class="rule-text" v-html="rule"></span>
            </div>
          </div>

          <h3 class="ds-group-title" style="margin-top:32px">For AI tools</h3>
          <p class="ds-section-desc">The repo includes a <code>CLAUDE.md</code> at the root. Claude Code, Cursor, and other AI tools automatically read this file for full design system context. Engineers don't need to paste the token list manually — it's always available in the codebase.</p>
          <div class="code-block">
            <div class="code-block-header">
              <span>CLAUDE.md — auto-loaded by AI tools in this repo</span>
              <span class="code-block-path">palmstreet-prototype-playground/CLAUDE.md</span>
            </div>
            <pre class="code-block-body code-block-body--scroll">{{ claudeContextDoc }}</pre>
          </div>
        </section>

        <!-- ══════════════════════════════════════════
             COLORS
        ══════════════════════════════════════════ -->
        <section :id="sections[1].id" class="ds-section">
          <h2 class="ds-section-title">Semantic color</h2>
          <p class="ds-section-desc">Three token groups. Always use <code>var(--token-name)</code>. Never use base palette values or hex directly in components.</p>

          <div class="token-table">
            <!-- Table header -->
            <div class="token-table-head">
              <span class="tcol-name">Name</span>
              <span class="tcol-mode">Light</span>
              <span class="tcol-mode">Dark</span>
              <span class="tcol-use">Use</span>
            </div>

            <!-- ── Content ── -->
            <div class="token-group-label">Content</div>
            <div
              v-for="t in contentTokens"
              :key="t.name"
              class="token-row"
              @click="copyToken(t.name)"
            >
              <div class="tcol-name">
                <span class="trow-icon">T</span>
                <code class="token-name">{{ t.name }}</code>
              </div>
              <div class="tcol-mode">
                <div
                  class="token-mini-swatch"
                  :style="{ background: t.lightHex, boxShadow: isLightHex(t.lightHex) ? 'inset 0 0 0 1px rgba(0,0,0,0.12)' : 'none' }"
                />
                <span class="trow-base">{{ t.lightBase }}</span>
                <strong class="trow-hex">{{ t.lightHex }}</strong>
              </div>
              <div class="tcol-mode">
                <div
                  class="token-mini-swatch token-mini-swatch--on-dark"
                  :style="{ background: t.darkHex, boxShadow: isLightHex(t.darkHex) ? 'inset 0 0 0 1px rgba(0,0,0,0.12)' : 'none' }"
                />
                <span class="trow-base">{{ t.darkBase }}</span>
                <strong class="trow-hex">{{ t.darkHex }}</strong>
              </div>
              <span class="tcol-use">{{ t.desc }}</span>
              <span v-if="copiedToken === t.name" class="copy-badge">Copied!</span>
            </div>

            <!-- ── Section ── -->
            <div class="token-group-label">Section</div>
            <div
              v-for="t in sectionTokens"
              :key="t.name"
              class="token-row"
              @click="copyToken(t.name)"
            >
              <div class="tcol-name">
                <span class="trow-icon">T</span>
                <code class="token-name">{{ t.name }}</code>
              </div>
              <div class="tcol-mode">
                <div
                  class="token-mini-swatch"
                  :style="{ background: t.lightHex, boxShadow: isLightHex(t.lightHex) ? 'inset 0 0 0 1px rgba(0,0,0,0.12)' : 'none' }"
                />
                <span class="trow-base">{{ t.lightBase }}</span>
                <strong class="trow-hex">{{ t.lightHex }}</strong>
              </div>
              <div class="tcol-mode">
                <div
                  class="token-mini-swatch token-mini-swatch--on-dark"
                  :style="{ background: t.darkHex, boxShadow: isLightHex(t.darkHex) ? 'inset 0 0 0 1px rgba(0,0,0,0.12)' : 'none' }"
                />
                <span class="trow-base">{{ t.darkBase }}</span>
                <strong class="trow-hex">{{ t.darkHex }}</strong>
              </div>
              <span class="tcol-use">{{ t.desc }}</span>
              <span v-if="copiedToken === t.name" class="copy-badge">Copied!</span>
            </div>

            <!-- ── Line ── -->
            <div class="token-group-label">Line</div>
            <div
              v-for="t in lineTokens"
              :key="t.name"
              class="token-row"
              @click="copyToken(t.name)"
            >
              <div class="tcol-name">
                <span class="trow-icon">T</span>
                <code class="token-name">{{ t.name }}</code>
              </div>
              <div class="tcol-mode">
                <div
                  class="token-mini-swatch"
                  :style="{ background: t.lightHex, boxShadow: isLightHex(t.lightHex) ? 'inset 0 0 0 1px rgba(0,0,0,0.12)' : 'none' }"
                />
                <span class="trow-base">{{ t.lightBase }}</span>
                <strong class="trow-hex">{{ t.lightHex }}</strong>
              </div>
              <div class="tcol-mode">
                <div
                  class="token-mini-swatch token-mini-swatch--on-dark"
                  :style="{ background: t.darkHex, boxShadow: isLightHex(t.darkHex) ? 'inset 0 0 0 1px rgba(0,0,0,0.12)' : 'none' }"
                />
                <span class="trow-base">{{ t.darkBase }}</span>
                <strong class="trow-hex">{{ t.darkHex }}</strong>
              </div>
              <span class="tcol-use">{{ t.desc }}</span>
              <span v-if="copiedToken === t.name" class="copy-badge">Copied!</span>
            </div>
          </div>
        </section>

        <!-- ══════════════════════════════════════════
             TYPOGRAPHY
        ══════════════════════════════════════════ -->
        <section :id="sections[2].id" class="ds-section">
          <h2 class="ds-section-title">Typography</h2>
          <p class="ds-section-desc">Gabarito only. Weights: Regular (400), Medium (500), SemiBold (600). Click any row to copy the CSS.</p>

          <div class="type-table">
            <div class="type-row type-header-row">
              <span class="type-col-name">Name</span>
              <span class="type-col-spec">Size / Weight</span>
              <span class="type-col-use">Use</span>
              <span class="type-col-example">Example</span>
            </div>
            <div
              v-for="t in typeScale"
              :key="t.name"
              class="type-row"
              @click="copyTypeSnippet(t)"
            >
              <span class="type-col-name"><code>{{ t.name }}</code></span>
              <span class="type-col-spec">{{ t.size }} / {{ t.weight }}</span>
              <span class="type-col-use">{{ t.use }}</span>
              <span class="type-col-example" :style="{ fontSize: t.size, fontWeight: t.weight }">
                {{ t.name }}
              </span>
              <span v-if="copiedType === t.name" class="copy-badge">Copied!</span>
            </div>
          </div>
        </section>

        <!-- ══════════════════════════════════════════
             SPACING
        ══════════════════════════════════════════ -->
        <section :id="sections[3].id" class="ds-section">
          <h2 class="ds-section-title">Spacing</h2>
          <p class="ds-section-desc">8px base grid. Use only these values. Use <code>9999px</code> for pill shapes.</p>

          <div class="spacing-grid">
            <div v-for="s in spacingScale" :key="s" class="spacing-item">
              <div class="spacing-bar-wrap">
                <div class="spacing-bar" :style="{ width: s + 'px', height: s + 'px', minWidth: '2px', minHeight: '2px' }"></div>
              </div>
              <code class="spacing-label">{{ s }}px</code>
            </div>
          </div>
        </section>

        <!-- ══════════════════════════════════════════
             COMPONENTS
        ══════════════════════════════════════════ -->
        <section :id="sections[4].id" class="ds-section">
          <h2 class="ds-section-title">Components</h2>

          <!-- Buttons -->
          <h3 class="ds-group-title">Button</h3>
          <p class="ds-section-desc">This section is implementation-first. Engineers should be able to copy the component or copy the current usage snippet directly. The controls on the right only change props for the same production-ready Vue component.</p>

          <div class="button-implementation">
            <div class="button-preview-card">
              <div class="button-preview-top">
                <div>
                  <div class="demo-label">Live implementation</div>
                  <h4 class="button-spec-title">`&lt;BranchButton /&gt;`</h4>
                </div>
                <button type="button" class="copy-action" @click="copySnippet(branchButtonUsageSnippet, 'BranchButton usage')">Copy usage</button>
              </div>

              <div class="button-preview-stage">
                <BranchButton
                  :variant="selectedButtonVariant"
                  :size="selectedButtonSize"
                  :state="selectedButtonState"
                >
                  {{ buttonLabel }}
                </BranchButton>
              </div>

              <div class="button-implementation-meta">
                <div v-for="item in activeButtonSpecs" :key="item.label" class="button-meta-pill">
                  <span>{{ item.label }}</span>
                  <strong>{{ item.value }}</strong>
                </div>
              </div>

              <div class="code-block button-code-block">
                <div class="code-block-header">
                  <span>Usage snippet</span>
                  <span class="code-block-path">pages / components</span>
                </div>
                <pre class="code-block-body">{{ branchButtonUsageSnippet }}</pre>
              </div>
            </div>

            <div class="button-controls-card">
              <div class="demo-label">Options</div>
              <h4 class="button-spec-title">Implementation controls</h4>

              <div class="button-control-group">
                <span class="button-control-label">Variant</span>
                <div class="segmented-control">
                  <button
                    v-for="option in buttonVariantOptions"
                    :key="option.value"
                    type="button"
                    class="segmented-control__item"
                    :class="{ 'segmented-control__item--active': selectedButtonVariant === option.value }"
                    @click="selectedButtonVariant = option.value"
                  >
                    {{ option.label }}
                  </button>
                </div>
              </div>

              <div class="button-control-group">
                <span class="button-control-label">Size</span>
                <div class="segmented-control">
                  <button
                    v-for="option in buttonSizeOptions"
                    :key="option.value"
                    type="button"
                    class="segmented-control__item"
                    :class="{ 'segmented-control__item--active': selectedButtonSize === option.value }"
                    @click="selectedButtonSize = option.value"
                  >
                    {{ option.label }}
                  </button>
                </div>
              </div>

              <div class="button-control-group">
                <span class="button-control-label">State</span>
                <div class="segmented-control segmented-control--stacked">
                  <button
                    v-for="option in buttonStateOptions"
                    :key="option.value"
                    type="button"
                    class="segmented-control__item"
                    :class="{ 'segmented-control__item--active': selectedButtonState === option.value }"
                    @click="selectedButtonState = option.value"
                  >
                    {{ option.label }}
                  </button>
                </div>
              </div>

              <label class="button-text-field">
                <span class="button-control-label">Label</span>
                <input v-model="buttonLabel" class="button-input" />
              </label>

              <div class="button-copy-actions">
                <button type="button" class="copy-action" @click="copySnippet(branchButtonSource, 'BranchButton component')">Copy component</button>
                <button type="button" class="copy-action" @click="copySnippet(branchButtonUsageSnippet, 'BranchButton usage')">Copy current example</button>
              </div>
            </div>
          </div>

          <div class="button-implementation-grid">
            <div class="button-implementation-card">
              <div class="button-preview-top">
                <div>
                  <div class="demo-label">Production file</div>
                  <h4 class="button-spec-title">Component source</h4>
                </div>
                <span class="button-spec-chip">`components/BranchButton.vue`</span>
              </div>
              <div class="code-block button-code-block">
                <div class="code-block-header">
                  <span>BranchButton.vue</span>
                  <button type="button" class="copy-inline" @click="copySnippet(branchButtonSource, 'BranchButton component')">Copy</button>
                </div>
                <pre class="code-block-body code-block-body--scroll">{{ branchButtonSource }}</pre>
              </div>
            </div>

            <div class="button-implementation-card">
              <div class="button-preview-top">
                <div>
                  <div class="demo-label">API</div>
                  <h4 class="button-spec-title">Props engineers should use</h4>
                </div>
              </div>

              <div class="button-api-list">
                <div v-for="item in buttonApi" :key="item.name" class="button-api-row">
                  <div class="button-api-head">
                    <code>{{ item.name }}</code>
                    <span class="button-api-type">{{ item.type }}</span>
                  </div>
                  <p class="button-spec-text">{{ item.description }}</p>
                </div>
              </div>
            </div>
          </div>

          <!-- Badges -->
          <h3 class="ds-group-title" style="margin-top:32px">Badge</h3>
          <div class="component-row">
            <div class="component-demo">
              <div class="demo-label">Success</div>
              <span class="badge-demo success">Available</span>
              <code class="demo-code">--section-success · --content-success</code>
            </div>
            <div class="component-demo">
              <div class="demo-label">Destructive</div>
              <span class="badge-demo destructive">Sold out</span>
              <code class="demo-code">--section-destructive · --content-destructive</code>
            </div>
            <div class="component-demo">
              <div class="demo-label">Warning</div>
              <span class="badge-demo warning">Low stock</span>
              <code class="demo-code">--section-warning · --content-warning</code>
            </div>
          </div>

          <!-- Input -->
          <h3 class="ds-group-title" style="margin-top:32px">Input Field</h3>
          <div class="component-row">
            <div class="component-demo">
              <div class="demo-label">Default</div>
              <div class="input-demo">
                <label class="input-label">Title</label>
                <input class="input-field" placeholder="Monstera albo" />
              </div>
              <code class="demo-code">bg: --section-floor-1<br>border: --border</code>
            </div>
            <div class="component-demo">
              <div class="demo-label">Active</div>
              <div class="input-demo">
                <label class="input-label">Title</label>
                <input class="input-field input-active" value="Monstera albo" />
              </div>
              <code class="demo-code">border: --border-strong</code>
            </div>
            <div class="component-demo">
              <div class="demo-label">Error</div>
              <div class="input-demo">
                <label class="input-label">Title</label>
                <input class="input-field input-error" value="x" />
                <span class="input-error-msg">Title is too short</span>
              </div>
              <code class="demo-code">border: --border-destructive<br>msg: --content-destructive</code>
            </div>
          </div>

          <!-- Icons -->
          <h3 class="ds-group-title" style="margin-top:32px">Icons</h3>
          <p class="ds-section-desc" style="margin-bottom:16px">Google Material Design Icons · Filled · Rounded only. Color always via a Content token.</p>
          <div class="icon-grid">
            <div v-for="icon in exampleIcons" :key="icon.name" class="icon-demo">
              <span class="material-symbols-rounded" style="font-size:28px; color: var(--content-primary)">{{ icon.name }}</span>
              <code>{{ icon.name }}</code>
            </div>
          </div>
        </section>

      </main>
    </div>

    <!-- Copy toast -->
    <div v-if="showCopyToast" class="copy-toast">
      <span class="material-symbols-rounded" style="font-size:18px; color: var(--content-success)">check_circle</span>
      <span>{{ copiedText }} copied</span>
    </div>

  </div>
</template>

<script setup>
import { computed, ref, onMounted, onUnmounted } from 'vue'

const theme = inject('theme')
const toggleTheme = inject('toggleTheme')

const copiedToken = ref(null)
const copiedType = ref(null)
const showCopyToast = ref(false)
const copiedText = ref('')
const activeSection = ref('colors')

const sections = [
  { id: 'rules',      label: 'Rules' },
  { id: 'colors',     label: 'Colors' },
  { id: 'typography', label: 'Typography' },
  { id: 'spacing',    label: 'Spacing' },
  { id: 'components', label: 'Components' },
]

const claudeContextDoc = `# Palmstreet Prototype Playground — Claude Context

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
- **Typeface:** Gabarito only — loaded via Google Fonts in \`nuxt.config.ts\`
- **Icons:** Google Material Design Icons, filled + rounded variant only
- **No component library** — build from scratch using tokens below

---

## File Structure Rules

Each designer gets their own folder under \`/pages/\`:

\`\`\`
/pages
  /yufan/              ← Yufan's prototypes
    my-prototype.vue   ← becomes /yufan/my-prototype
  /[designer-name]/    ← other designers
  /shared/             ← shared example components and utilities
/components/           ← reusable prototype components
/styles/
  tokens.css           ← ALL Branch DS semantic tokens (source of truth)
  base.css             ← reset, Gabarito font, body defaults
\`\`\`

**Never put prototype files directly in \`/pages/\` root.** Always inside a designer subfolder.

---

## Design System Tokens

All tokens live in \`/styles/tokens.css\`. Always use \`var(--token-name)\`.
Never use raw hex values. Never reference base/global token names.

### Content Tokens (text, icons — use with \`color\`, \`fill\`, \`stroke\`)
\`\`\`
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
\`\`\`

### Section Tokens (surfaces — use with \`background-color\`)
\`\`\`
--section-basement         Base page background
--section-floor-1          Cards, panels, containers
--section-floor-2          Popovers, dropdowns, floating UI
--section-basement-inverse Inverted base background
--section-floor-1-inverse  Inverted card surface (used by Toast)
--section-interactive      Hover and focus surfaces
--section-destructive      Error backgrounds
--section-success          Success backgrounds
--section-warning          Warning backgrounds
--section-cta              #B9EC51 — primary CTA only, never decorative
--section-always-white     Always white surface
--section-always-black     Always black surface
--scrim-heavy              Modal overlays
--scrim-light              Drawer overlays
\`\`\`

### Line Tokens (borders — use with \`border-color\`)
\`\`\`
--border                  Default borders and dividers
--border-strong           Focused inputs, emphasized borders
--border-destructive      Error input borders
--border-success          Success input borders
\`\`\`

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

8px grid. Use only: \`0, 2, 4, 8, 12, 16, 24, 32, 40, 48\`. Use \`9999px\` for pill shapes.

---

## Platform Rules

**Desktop:** sidebar nav + main content, no fixed width
**Mobile:** 375px base, full-width stacked layout
- \`Docked Button\` (375×98px) — mobile-only fixed bottom action bar
- \`Page Title\` (375×92px) — mobile-only top nav bar with back arrow

---

## Component Rules

### Button
- Primary: \`background: var(--section-cta)\`, label: \`var(--content-always-black)\` — ALWAYS always-black, never content-primary
- Secondary: \`background: var(--section-floor-1)\`, border: \`var(--border)\`, label: \`var(--content-primary)\`
- Destructive: \`background: var(--section-destructive)\`, label: \`var(--content-always-white)\`
- Height: 40px (medium), 32px (small)

### Toast
- ALWAYS uses inverse tokens regardless of theme
- Background: \`var(--section-floor-1-inverse)\`, text: \`var(--content-primary-inverse)\`

### Icons
- Google Material Icons, filled + rounded ONLY
- Color always set via a Content token, never hardcoded

---

## Light / Dark Mode

Dark mode is handled entirely via CSS variable swapping at \`:root[data-theme="dark"]\`.
No component-level overrides needed. The same token names work in both modes.

---

## Rules for Code Generation

1. Never use raw hex or base token names — always \`var(--token-name)\`
2. Never use any typeface other than Gabarito
3. Always implement both light and dark mode via the token layer
4. Follow the 8px spacing grid
5. Primary button label is always \`--content-always-black\`
6. Toast always uses inverse tokens
7. Docked Button and Page Title are mobile-only
8. Icons: Google Material Design, filled, rounded only
9. Icon color is always a Content token
10. No generic SaaS aesthetics — neutral, marketplace character

---

## Adding a New Prototype

1. Create \`/pages/[your-name]/[prototype-name].vue\`
2. Use tokens from \`/styles/tokens.css\`
3. Keep it self-contained — inline styles are fine for prototypes
4. Push your branch — Vercel auto-generates a preview URL
5. Share the URL in Slack or Linear for engineer review`

// ─── Token lists ──────────────────────────────────────
// lightBase / darkBase → Tier-1 palette reference for each mode (Figma naming)
// lightHex  / darkHex  → resolved color values

const contentTokens = [
  { name: '--content-primary',           lightBase: 'Gray/800',        lightHex: '#1B1B1B', darkBase: 'Gray/50',         darkHex: '#F8F8F8', desc: 'Primary text and icons' },
  { name: '--content-secondary',         lightBase: 'Gray/500',        lightHex: '#555555', darkBase: 'Gray/300',        darkHex: '#999999', desc: 'Supporting text' },
  { name: '--content-tertiary',          lightBase: 'Gray/400',        lightHex: '#777777', darkBase: 'Gray/400',        darkHex: '#777777', desc: 'Muted text, placeholders' },
  { name: '--content-quaternary',        lightBase: 'Gray/200',        lightHex: '#BBBBBB', darkBase: 'Gray/500',        darkHex: '#555555', desc: 'Disabled states' },
  { name: '--content-primary-inverse',   lightBase: 'Gray/50',         lightHex: '#F8F8F8', darkBase: 'Gray/800',        darkHex: '#1B1B1B', desc: 'Primary text on dark surfaces' },
  { name: '--content-secondary-inverse', lightBase: 'Gray/300',        lightHex: '#999999', darkBase: 'Gray/500',        darkHex: '#555555', desc: 'Secondary text on dark surfaces' },
  { name: '--content-always-white',      lightBase: 'Primary/White',   lightHex: '#FFFFFF', darkBase: 'Primary/White',   darkHex: '#FFFFFF', desc: 'Always white — never flips' },
  { name: '--content-always-black',      lightBase: 'Primary/Black',   lightHex: '#000000', darkBase: 'Primary/Black',   darkHex: '#000000', desc: 'Always black — never flips' },
  { name: '--content-interactive',       lightBase: 'Function/Blue-200', lightHex: '#2269EC', darkBase: 'Function/Blue-100', darkHex: '#4D8EF5', desc: 'Links, active tabs, selected' },
  { name: '--content-destructive',       lightBase: 'Function/Red-200',  lightHex: '#E53935', darkBase: 'Function/Red-100',  darkHex: '#EF5350', desc: 'Error text and icons' },
  { name: '--content-success',           lightBase: 'Function/Green-100', lightHex: '#43A047', darkBase: 'Function/Green-100', darkHex: '#66BB6A', desc: 'Success text and icons' },
  { name: '--content-warning',           lightBase: 'Function/Yellow-200', lightHex: '#FB8C00', darkBase: 'Function/Yellow-100', darkHex: '#FFA726', desc: 'Warning text and icons' },
]

const sectionTokens = [
  { name: '--section-basement',          lightBase: 'Gray/0',           lightHex: '#FFFFFF',               darkBase: 'Gray/800',          darkHex: '#1B1B1B',               desc: 'Base page background' },
  { name: '--section-floor-1',           lightBase: 'Gray/50',          lightHex: '#F8F8F8',               darkBase: 'Gray/700',          darkHex: '#252525',               desc: 'Cards, panels, containers' },
  { name: '--section-floor-2',           lightBase: 'Gray/100',         lightHex: '#DDDDDD',               darkBase: 'Gray/2C',           darkHex: '#2C2C2C',               desc: 'Popovers, dropdowns, floating UI' },
  { name: '--section-basement-inverse',  lightBase: 'Gray/800',         lightHex: '#1B1B1B',               darkBase: 'Gray/0',            darkHex: '#FFFFFF',               desc: 'Inverted base background' },
  { name: '--section-floor-1-inverse',   lightBase: 'Gray/700',         lightHex: '#252525',               darkBase: 'Gray/50',           darkHex: '#F8F8F8',               desc: 'Inverted card surface — used by Toast' },
  { name: '--section-interactive',       lightBase: 'Function/Blue-200',   lightHex: '#2269EC', darkBase: 'Function/Blue-200',   darkHex: '#2269EC', desc: 'Hover and focus states' },
  { name: '--section-destructive',       lightBase: 'Function/Red-200',    lightHex: '#E53935', darkBase: 'Function/Red-200',    darkHex: '#E53935', desc: 'Error backgrounds' },
  { name: '--section-success',           lightBase: 'Function/Green-100',  lightHex: '#43A047', darkBase: 'Function/Green-200',  darkHex: '#66BB6A', desc: 'Success backgrounds' },
  { name: '--section-warning',           lightBase: 'Function/Yellow-200', lightHex: '#FB8C00', darkBase: 'Function/Yellow-200', darkHex: '#FB8C00', desc: 'Warning backgrounds' },
  { name: '--section-cta',               lightBase: 'Brand/Neon-Green', lightHex: '#B9EC51',               darkBase: 'Brand/Neon-Green',  darkHex: '#B9EC51',               desc: 'Primary CTA only — never changes' },
  { name: '--section-always-white',      lightBase: 'Primary/White',    lightHex: '#FFFFFF',               darkBase: 'Primary/White',     darkHex: '#FFFFFF',               desc: 'Always white surface' },
  { name: '--section-always-black',      lightBase: 'Primary/Black',    lightHex: '#000000',               darkBase: 'Primary/Black',     darkHex: '#000000',               desc: 'Always black surface' },
]

const lineTokens = [
  { name: '--border',             lightBase: 'Gray/100',           lightHex: '#DDDDDD', darkBase: 'Gray/2C',             darkHex: '#2C2C2C', desc: 'Default borders and dividers' },
  { name: '--border-strong',      lightBase: 'Gray/800',           lightHex: '#1B1B1B', darkBase: 'Gray/50',             darkHex: '#F8F8F8', desc: 'Focused inputs, emphasized borders' },
  { name: '--border-destructive', lightBase: 'Function/Red-200',   lightHex: '#E53935', darkBase: 'Function/Red-100',    darkHex: '#EF5350', desc: 'Error input borders' },
  { name: '--border-success',     lightBase: 'Function/Green-200', lightHex: '#43A047', darkBase: 'Function/Green-100',  darkHex: '#66BB6A', desc: 'Success input borders' },
]

// Returns true for near-white hex values that need a visible border on light backgrounds
function isLightHex(hex) {
  return ['#FFFFFF', '#F8F8F8', '#DDDDDD'].includes(hex.toUpperCase())
}

// ─── Type scale ───────────────────────────────────────
const typeScale = [
  { name: 'Display0',   size: '70px', weight: '500', use: 'Hero' },
  { name: 'Display1',   size: '57px', weight: '600', use: 'Large display' },
  { name: 'Headline1',  size: '32px', weight: '400', use: 'Page headlines' },
  { name: 'Headline2',  size: '28px', weight: '400', use: 'Section headlines' },
  { name: 'Headline3',  size: '24px', weight: '600', use: 'Sub-headlines' },
  { name: 'Title1',     size: '22px', weight: '500', use: 'Titles' },
  { name: 'Title2',     size: '18px', weight: '600', use: 'Page titles (mobile nav)' },
  { name: 'Title3',     size: '16px', weight: '500', use: 'Section titles' },
  { name: 'Body1',      size: '16px', weight: '400', use: 'Primary body text' },
  { name: 'Body2',      size: '14px', weight: '400', use: 'Secondary body text' },
  { name: 'Label1',     size: '12px', weight: '600', use: 'Input labels, tags' },
  { name: 'Label2',     size: '10px', weight: '600', use: 'Small labels' },
  { name: 'Button1',    size: '16px', weight: '500', use: 'Button text (medium)' },
  { name: 'Button2',    size: '14px', weight: '500', use: 'Button text (small)' },
]

// ─── Spacing scale ────────────────────────────────────
const spacingScale = [0, 2, 4, 8, 12, 16, 24, 32, 40, 48]

// ─── Button implementation docs ───────────────────────
const buttonVariantOptions = [
  { label: 'Primary', value: 'primary' },
  { label: 'Secondary', value: 'secondary' },
  { label: 'Destructive', value: 'destructive' },
]

const buttonSizeOptions = [
  { label: 'Small', value: 'sm' },
  { label: 'Medium', value: 'md' },
]

const buttonStateOptions = [
  { label: 'Default', value: 'default' },
  { label: 'Hovered', value: 'hovered' },
  { label: 'Focused', value: 'focused' },
  { label: 'Disabled', value: 'disabled' },
]

const buttonApi = [
  { name: 'variant', type: `'primary' | 'secondary' | 'destructive'`, description: 'Chooses the visual treatment and emphasis level.' },
  { name: 'size', type: `'sm' | 'md'`, description: 'Uses the Palmstreet button heights and typography tokens.' },
  { name: 'state', type: `'default' | 'hovered' | 'focused' | 'disabled'`, description: 'Useful for docs, QA, or forced visual snapshots.' },
  { name: 'disabled', type: 'boolean', description: 'Native disabled behavior. Also applied automatically when state is disabled.' },
  { name: 'type', type: `'button' | 'submit' | 'reset'`, description: 'Pass-through HTML button type for production forms.' },
]

const selectedButtonVariant = ref('primary')
const selectedButtonSize = ref('md')
const selectedButtonState = ref('default')
const buttonLabel = ref('Create Listing')

const buttonSpecLookup = {
  primary: {
    variant: 'Primary',
    container: 'var(--section-cta)',
    content: 'var(--content-always-black)',
  },
  secondary: {
    variant: 'Secondary',
    container: 'var(--section-floor-1)',
    content: 'var(--content-primary)',
  },
  destructive: {
    variant: 'Destructive',
    container: 'var(--content-destructive)',
    content: 'var(--content-always-white)',
  },
}

const buttonSizeLookup = {
  sm: { label: 'Small', height: '32px', text: 'Button2 · 14px', padding: '0 12px' },
  md: { label: 'Medium', height: '40px', text: 'Button1 · 16px', padding: '0 16px' },
}

const activeButtonSpecs = computed(() => {
  const variant = buttonSpecLookup[selectedButtonVariant.value]
  const size = buttonSizeLookup[selectedButtonSize.value]

  return [
    { label: 'Variant', value: variant.variant },
    { label: 'State', value: selectedButtonState.value },
    { label: 'Height', value: size.height },
    { label: 'Type', value: size.text },
    { label: 'Container', value: variant.container },
    { label: 'Label', value: variant.content },
  ]
})

const branchButtonUsageSnippet = computed(() => {
  const props = []

  if (selectedButtonVariant.value !== 'primary') props.push(`variant="${selectedButtonVariant.value}"`)
  if (selectedButtonSize.value !== 'md') props.push(`size="${selectedButtonSize.value}"`)
  if (selectedButtonState.value !== 'default') props.push(`state="${selectedButtonState.value}"`)

  const propString = props.length ? ` ${props.join(' ')}` : ''
  return `<BranchButton${propString}>
  ${buttonLabel.value}
</BranchButton>`
})

const branchButtonSource = `<template>
  <button
    class="branch-button"
    :class="buttonClassNames"
    :type="type"
    :disabled="isDisabled"
  >
    <slot />
  </button>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  variant: {
    type: String,
    default: 'primary',
    validator: (value) => ['primary', 'secondary', 'destructive'].includes(value),
  },
  size: {
    type: String,
    default: 'md',
    validator: (value) => ['sm', 'md'].includes(value),
  },
  state: {
    type: String,
    default: 'default',
    validator: (value) => ['default', 'hovered', 'focused', 'disabled'].includes(value),
  },
  disabled: {
    type: Boolean,
    default: false,
  },
  type: {
    type: String,
    default: 'button',
  },
})

const isDisabled = computed(() => props.disabled || props.state === 'disabled')

const buttonClassNames = computed(() => [
  \`branch-button--\${props.variant}\`,
  \`branch-button--\${props.size}\`,
  props.state !== 'default' ? \`branch-button--\${props.state}\` : null,
])
<\/script>

<style scoped>
.branch-button {
  position: relative;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: fit-content;
  border: 1px solid transparent;
  border-radius: 8px;
  font-family: 'Gabarito', sans-serif;
  font-weight: 500;
  line-height: 1;
  white-space: nowrap;
  user-select: none;
  cursor: pointer;
  transition: box-shadow 120ms ease, opacity 120ms ease, background-color 120ms ease, border-color 120ms ease;
}

.branch-button::after {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: inherit;
  opacity: 0;
  pointer-events: none;
  transition: opacity 120ms ease;
}

.branch-button--primary {
  background: var(--section-cta);
  color: var(--content-always-black);
}

.branch-button--secondary {
  background: var(--section-floor-1);
  border-color: var(--border);
  color: var(--content-primary);
}

.branch-button--destructive {
  background: var(--content-destructive);
  color: var(--content-always-white);
}

.branch-button--sm {
  height: 32px;
  padding: 0 12px;
  font-size: 14px;
}

.branch-button--md {
  height: 40px;
  padding: 0 16px;
  font-size: 16px;
}

.branch-button:hover::after,
.branch-button--hovered::after {
  background: rgba(0, 0, 0, 0.08);
  opacity: 1;
}

.branch-button:focus-visible,
.branch-button--focused {
  outline: none;
  box-shadow: 0 0 0 3px rgba(34, 105, 236, 0.24);
}

.branch-button:disabled,
.branch-button--disabled {
  opacity: 0.4;
  cursor: default;
  pointer-events: none;
}
</style>`

// ─── Example icons ────────────────────────────────────
const exampleIcons = [
  { name: 'add' }, { name: 'close' }, { name: 'arrow_back' },
  { name: 'search' }, { name: 'more_horiz' }, { name: 'check_circle' },
  { name: 'delete' }, { name: 'edit' }, { name: 'expand_more' },
  { name: 'local_florist' }, { name: 'storefront' }, { name: 'inventory_2' },
  { name: 'local_shipping' }, { name: 'attach_money' }, { name: 'visibility' },
]

// ─── Rules ────────────────────────────────────────────
const rules = [
  'Never use raw hex or base token names — always <code>var(--token-name)</code>',
  'Never use any typeface other than <strong>Gabarito</strong>',
  'Always implement both light and dark mode via the semantic token layer',
  'Spacing follows the <strong>8px grid</strong>: 0, 2, 4, 8, 12, 16, 24, 32, 40, 48',
  'Primary button labels always use <code>--content-always-black</code>, not <code>--content-primary</code>',
  'Toast always uses inverse tokens — never apply theme tokens to Toast',
  'Docked Button and Page Title are <strong>mobile-only</strong> — never use on desktop',
  'Icons: Google Material Design, <strong>filled, rounded</strong> corners only',
  'Icon color is always a Content token — never hardcoded',
  'No generic SaaS or tech aesthetics — neutral, modern, marketplace character',
]

// ─── Copy helpers ─────────────────────────────────────
function showCopy(text) {
  copiedText.value = text
  showCopyToast.value = true
  setTimeout(() => { showCopyToast.value = false }, 1800)
}

function copyToken(name) {
  navigator.clipboard?.writeText(`var(${name})`)
  copiedToken.value = name
  showCopy(`var(${name})`)
  setTimeout(() => { copiedToken.value = null }, 1500)
}

function copyTypeSnippet(t) {
  const snippet = `font-size: ${t.size}; font-weight: ${t.weight};`
  navigator.clipboard?.writeText(snippet)
  copiedType.value = t.name
  showCopy(t.name)
  setTimeout(() => { copiedType.value = null }, 1500)
}

function copySnippet(snippet, label) {
  navigator.clipboard?.writeText(snippet)
  showCopy(label)
}

// ─── Active section tracking ──────────────────────────
function onScroll() {
  for (const s of [...sections].reverse()) {
    const el = document.getElementById(s.id)
    if (el && el.getBoundingClientRect().top <= 120) {
      activeSection.value = s.id
      return
    }
  }
  activeSection.value = sections[0].id
}

onMounted(() => window.addEventListener('scroll', onScroll))
onUnmounted(() => window.removeEventListener('scroll', onScroll))
</script>

<style scoped>
/* ─── Root ─── */
.ds-root { font-family: 'Gabarito', sans-serif; background: var(--section-basement); color: var(--content-primary); min-height: 100vh; }

/* ─── Proto bar ─── */
.proto-bar { display: flex; align-items: center; justify-content: space-between; padding: 0 24px; height: 48px; border-bottom: 1px solid var(--border); background: var(--section-floor-1); position: sticky; top: 0; z-index: 30; }
.back-link { display: flex; align-items: center; gap: 6px; font-size: 14px; color: var(--content-interactive); text-decoration: none; }
.proto-label { font-size: 14px; font-weight: 600; color: var(--content-primary); }
.proto-toggle { width: 32px; height: 32px; border-radius: 8px; border: 1px solid var(--border); background: var(--section-basement); cursor: pointer; display: flex; align-items: center; justify-content: center; color: var(--content-primary); }

/* ─── Layout ─── */
.ds-layout { display: flex; max-width: 1200px; margin: 0 auto; padding: 0 32px; gap: 48px; }

/* ─── Sidebar ─── */
.ds-nav { width: 160px; flex-shrink: 0; padding-top: 48px; position: sticky; top: 64px; align-self: flex-start; height: calc(100vh - 64px); }
.nav-link { display: block; padding: 8px 12px; font-size: 14px; font-weight: 500; color: var(--content-secondary); text-decoration: none; border-radius: 8px; margin-bottom: 2px; transition: background 0.1s, color 0.1s; }
.nav-link:hover { background: var(--section-floor-1); color: var(--content-primary); }
.nav-link.active { background: var(--section-floor-1); color: var(--content-interactive); }

/* ─── Main ─── */
.ds-main { flex: 1; min-width: 0; padding: 48px 0 120px; }

/* ─── Hero ─── */
.ds-hero { margin-bottom: 64px; padding-bottom: 48px; border-bottom: 1px solid var(--border); }
.ds-eyebrow { font-size: 12px; font-weight: 600; color: var(--content-tertiary); letter-spacing: 0.1em; text-transform: uppercase; margin-bottom: 8px; }
.ds-headline { font-size: 48px; font-weight: 600; color: var(--content-primary); margin-bottom: 16px; line-height: 1.1; }
.ds-sub { font-size: 16px; color: var(--content-secondary); max-width: 600px; line-height: 1.6; margin-bottom: 24px; }
.ds-meta-row { display: flex; flex-wrap: wrap; gap: 8px; }
.ds-chip { font-size: 12px; font-weight: 500; padding: 4px 10px; border-radius: 9999px; border: 1px solid var(--border); color: var(--content-secondary); background: var(--section-floor-1); }

/* ─── Section ─── */
.ds-section { margin-bottom: 80px; padding-top: 16px; }
.ds-section-title { font-size: 28px; font-weight: 600; color: var(--content-primary); margin-bottom: 8px; }
.ds-section-desc { font-size: 14px; color: var(--content-secondary); margin-bottom: 24px; line-height: 1.6; }
.ds-group-title { font-size: 16px; font-weight: 600; color: var(--content-primary); margin-bottom: 12px; display: flex; align-items: center; gap: 8px; }
.ds-group-note { font-size: 12px; font-weight: 400; color: var(--content-tertiary); }

/* ─── Color token table ─── */
.token-table { border: 1px solid var(--border); border-radius: 12px; overflow: hidden; }

/* Column grid: Name | Light | Dark | Use */
.token-table-head,
.token-row { display: grid; grid-template-columns: 220px 1fr 1fr 1fr; align-items: center; }

.token-table-head { padding: 10px 16px; background: var(--section-floor-1); border-bottom: 1px solid var(--border); }
.token-table-head span { font-size: 11px; font-weight: 600; color: var(--content-tertiary); text-transform: uppercase; letter-spacing: 0.07em; }

/* Group label row — spans all columns */
.token-group-label { grid-column: 1 / -1; padding: 10px 16px 6px; font-size: 12px; font-weight: 600; color: var(--content-tertiary); background: var(--section-floor-1); border-top: 1px solid var(--border); border-bottom: 1px solid var(--border); letter-spacing: 0.03em; }

.token-row { padding: 10px 16px; border-bottom: 1px solid var(--border); cursor: pointer; position: relative; transition: background 0.1s; }
.token-row:last-child { border-bottom: none; }
.token-row:hover { background: var(--section-floor-1); }

/* Name column */
.tcol-name { display: flex; align-items: center; gap: 8px; min-width: 0; padding-right: 12px; }
.trow-icon { width: 18px; height: 18px; border-radius: 4px; background: var(--section-floor-2); border: 1px solid var(--border); display: inline-flex; align-items: center; justify-content: center; font-size: 11px; font-weight: 700; color: var(--content-tertiary); font-family: 'Gabarito', sans-serif; flex-shrink: 0; line-height: 1; }
.token-name { font-size: 13px; font-weight: 600; color: #823FEE; font-family: 'Gabarito', sans-serif; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }

/* Light / Dark columns */
.tcol-mode { display: flex; align-items: center; gap: 8px; padding-right: 12px; min-width: 0; }
.token-mini-swatch { width: 18px; height: 18px; border-radius: 4px; flex-shrink: 0; }
.trow-base { font-size: 12px; color: var(--content-secondary); white-space: nowrap; flex-shrink: 0; }
.trow-hex { font-size: 13px; font-weight: 600; color: var(--content-primary); font-family: 'Gabarito', sans-serif; white-space: nowrap; }

/* Use column */
.tcol-use { font-size: 13px; color: var(--content-secondary); }

.copy-badge { position: absolute; right: 8px; top: 50%; transform: translateY(-50%); font-size: 11px; font-weight: 600; color: var(--content-success); background: var(--section-success); padding: 2px 8px; border-radius: 4px; }

/* ─── Type table ─── */
.type-table { border: 1px solid var(--border); border-radius: 12px; overflow: hidden; }
.type-row { display: grid; grid-template-columns: 140px 120px 1fr 1fr; gap: 16px; padding: 12px 16px; border-bottom: 1px solid var(--border); align-items: center; cursor: pointer; position: relative; transition: background 0.1s; }
.type-row:last-child { border-bottom: none; }
.type-row:hover:not(.type-header-row) { background: var(--section-floor-1); }
.type-header-row { background: var(--section-floor-1); cursor: default; }
.type-col-name, .type-col-spec, .type-col-use { font-size: 12px; }
.type-col-name code { font-family: monospace; font-size: 12px; color: var(--content-primary); }
.type-col-spec { color: var(--content-tertiary); }
.type-col-use { color: var(--content-secondary); }
.type-col-example { font-family: 'Gabarito', sans-serif; color: var(--content-primary); line-height: 1.2; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.type-header-row .type-col-name,
.type-header-row .type-col-spec,
.type-header-row .type-col-use,
.type-header-row .type-col-example { font-size: 11px; font-weight: 600; color: var(--content-tertiary); text-transform: uppercase; letter-spacing: 0.06em; }

/* ─── Spacing ─── */
.spacing-grid { display: flex; align-items: flex-end; gap: 16px; flex-wrap: wrap; padding: 24px; background: var(--section-floor-1); border-radius: 12px; border: 1px solid var(--border); }
.spacing-item { display: flex; flex-direction: column; align-items: center; gap: 8px; }
.spacing-bar-wrap { display: flex; align-items: flex-end; justify-content: center; height: 48px; }
.spacing-bar { background: var(--content-interactive); opacity: 0.3; border-radius: 3px; }
.spacing-label { font-size: 11px; font-weight: 600; color: var(--content-tertiary); font-family: monospace; }

/* ─── Components ─── */
.component-row { display: flex; flex-wrap: wrap; gap: 16px; }
.component-demo { display: flex; flex-direction: column; gap: 10px; padding: 16px; background: var(--section-floor-1); border: 1px solid var(--border); border-radius: 12px; min-width: 160px; }
.demo-label { font-size: 11px; font-weight: 600; color: var(--content-tertiary); text-transform: uppercase; letter-spacing: 0.06em; }
.demo-code { font-size: 11px; color: var(--content-tertiary); font-family: monospace; line-height: 1.5; white-space: pre; }

.button-implementation,
.button-implementation-grid { display: grid; gap: 16px; }
.button-implementation { grid-template-columns: minmax(0, 1.25fr) 320px; margin-bottom: 16px; }
.button-implementation-grid { grid-template-columns: repeat(2, minmax(0, 1fr)); }
.button-preview-card,
.button-controls-card,
.button-implementation-card { padding: 20px; background: var(--section-floor-1); border: 1px solid var(--border); border-radius: 16px; min-width: 0; }
.button-preview-top { display: flex; align-items: flex-start; justify-content: space-between; gap: 16px; margin-bottom: 16px; }
.button-spec-title { font-size: 18px; font-weight: 600; color: var(--content-primary); line-height: 1.3; }
.button-spec-text { font-size: 13px; color: var(--content-secondary); line-height: 1.6; }
.button-spec-chip { display: inline-flex; align-items: center; min-height: 28px; padding: 0 10px; border-radius: 9999px; background: var(--section-basement); border: 1px solid var(--border); font-size: 12px; font-weight: 500; color: var(--content-secondary); }
.button-preview-stage { display: flex; align-items: center; justify-content: center; min-height: 220px; padding: 32px; border-radius: 16px; background:
  radial-gradient(circle at top left, rgba(185, 236, 81, 0.12), transparent 30%),
  linear-gradient(180deg, var(--section-basement), var(--section-floor-1)); border: 1px solid var(--border); margin-bottom: 16px; }
.button-implementation-meta { display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 16px; }
.button-meta-pill { display: inline-flex; align-items: center; gap: 8px; min-height: 30px; padding: 0 10px; border-radius: 9999px; border: 1px solid var(--border); background: var(--section-basement); font-size: 12px; color: var(--content-secondary); }
.button-meta-pill strong { color: var(--content-primary); }
.button-code-block { margin-top: 0; }
.button-controls-card { display: flex; flex-direction: column; gap: 16px; }
.button-control-group { display: flex; flex-direction: column; gap: 8px; }
.button-control-label { font-size: 12px; font-weight: 600; color: var(--content-tertiary); text-transform: uppercase; letter-spacing: 0.05em; }
.segmented-control { display: flex; flex-wrap: wrap; gap: 8px; }
.segmented-control--stacked { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); }
.segmented-control__item,
.copy-action,
.copy-inline { appearance: none; border: 1px solid var(--border); background: var(--section-basement); color: var(--content-primary); font-family: 'Gabarito', sans-serif; font-size: 13px; font-weight: 500; border-radius: 10px; cursor: pointer; }
.segmented-control__item { min-height: 36px; padding: 0 12px; }
.segmented-control__item--active { background: var(--section-floor-2); border-color: var(--border-strong); }
.button-text-field { display: flex; flex-direction: column; gap: 8px; }
.button-input { width: 100%; height: 40px; padding: 0 12px; border-radius: 10px; border: 1px solid var(--border); background: var(--section-basement); color: var(--content-primary); font-family: 'Gabarito', sans-serif; font-size: 14px; }
.button-input:focus { outline: none; border-color: var(--border-strong); box-shadow: 0 0 0 3px rgba(34, 105, 236, 0.12); }
.button-copy-actions { display: flex; gap: 8px; flex-wrap: wrap; }
.copy-action { min-height: 36px; padding: 0 12px; }
.copy-inline { min-height: 28px; padding: 0 10px; }
.button-api-list { display: flex; flex-direction: column; gap: 12px; }
.button-api-row { padding-top: 12px; border-top: 1px solid var(--border); }
.button-api-row:first-child { padding-top: 0; border-top: none; }
.button-api-head { display: flex; align-items: center; justify-content: space-between; gap: 12px; margin-bottom: 6px; }
.button-api-type { font-size: 12px; color: var(--content-secondary); font-family: monospace; }

.badge-demo { display: inline-flex; align-items: center; padding: 4px 10px; border-radius: 4px; font-size: 12px; font-weight: 600; }
.badge-demo.success { background: var(--section-success); color: var(--content-success); }
.badge-demo.destructive { background: var(--section-destructive); color: var(--content-destructive); }
.badge-demo.warning { background: var(--section-warning); color: var(--content-warning); }

.input-demo { display: flex; flex-direction: column; gap: 4px; }
.input-label { font-size: 12px; font-weight: 600; color: var(--content-primary); }
.input-field { height: 40px; padding: 0 12px; background: var(--section-basement); border: 1px solid var(--border); border-radius: 8px; font-family: 'Gabarito', sans-serif; font-size: 16px; color: var(--content-primary); outline: none; width: 180px; }
.input-field::placeholder { color: var(--content-tertiary); }
.input-active { border-color: var(--border-strong); }
.input-error { border-color: var(--border-destructive); }
.input-error-msg { font-size: 12px; color: var(--content-destructive); }

.icon-grid { display: flex; flex-wrap: wrap; gap: 8px; }
.icon-demo { display: flex; flex-direction: column; align-items: center; gap: 6px; padding: 12px 16px; border: 1px solid var(--border); border-radius: 8px; background: var(--section-floor-1); }
.icon-demo code { font-size: 10px; color: var(--content-tertiary); font-family: monospace; }

/* ─── Rules ─── */
.rules-list { display: flex; flex-direction: column; gap: 8px; }
.rule-item { display: flex; gap: 12px; padding: 12px 16px; background: var(--section-floor-1); border: 1px solid var(--border); border-radius: 8px; align-items: flex-start; }
.rule-num { font-size: 12px; font-weight: 600; color: var(--content-tertiary); min-width: 20px; padding-top: 1px; }
.rule-text { font-size: 14px; color: var(--content-primary); line-height: 1.5; }

.code-block { border: 1px solid var(--border); border-radius: 12px; overflow: hidden; margin-top: 16px; }
.code-block-header { display: flex; align-items: center; justify-content: space-between; padding: 10px 16px; background: var(--section-floor-2); font-size: 12px; font-weight: 600; color: var(--content-secondary); }
.code-block-path { font-family: monospace; font-weight: 400; color: var(--content-tertiary); }
.code-block-body { margin: 0; padding: 16px; background: var(--section-floor-1); font-family: monospace; font-size: 12px; color: #823FEE; line-height: 1.7; overflow-x: auto; white-space: pre; }
.code-block-body--scroll { max-height: 420px; overflow: auto; }

/* ─── Copy toast ─── */
.copy-toast { position: fixed; bottom: 24px; left: 50%; transform: translateX(-50%); display: flex; align-items: center; gap: 8px; padding: 10px 16px; background: var(--section-floor-1-inverse); color: var(--content-primary-inverse); border-radius: 9999px; font-size: 13px; font-weight: 500; z-index: 100; animation: fade-up 0.2s ease; white-space: nowrap; }
@keyframes fade-up { from { opacity: 0; transform: translateX(-50%) translateY(8px); } to { opacity: 1; transform: translateX(-50%) translateY(0); } }

code { font-family: monospace; font-size: 12px; color: #823FEE; background: none; padding: 0; }
strong { font-weight: 600; }

@media (max-width: 1100px) {
  .button-implementation,
  .button-implementation-grid { grid-template-columns: 1fr; }
}

@media (max-width: 760px) {
  .button-preview-top,
  .button-api-head { flex-direction: column; align-items: flex-start; }
  .segmented-control--stacked { grid-template-columns: 1fr; }
}
</style>
