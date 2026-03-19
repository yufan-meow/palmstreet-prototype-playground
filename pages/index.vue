<template>
  <div class="playground">
    <header class="header">
      <div class="header-shell">
        <div class="header-left">
          <div class="brand-mark">🌴</div>
          <div>
            <p class="header-eyebrow">Palmstreet Design Team</p>
            <h1 class="header-title">Prototype Playground</h1>
          </div>
        </div>

        <div class="header-actions">
          <label class="search-field">
            <span class="material-symbols-rounded search-icon">search</span>
            <input
              v-model="searchQuery"
              type="search"
              placeholder="Search prototypes, teams, or route slugs"
            >
          </label>

          <button class="theme-toggle" @click="toggleTheme()">
            <span class="material-symbols-rounded" style="font-size:20px">{{ theme === 'dark' ? 'light_mode' : 'dark_mode' }}</span>
          </button>
        </div>
      </div>

      <div class="hero">
        <div class="hero-copy">
          <p class="hero-kicker">Design Prototype Library</p>
          <h2 class="hero-title">Browse the real work, not placeholders.</h2>
          <p class="hero-sub">
            The playground now auto-discovers prototypes, lets you search across teams and pages, and renders live previews from the actual routes so cards reflect the designs they link to.
          </p>
        </div>

        <div class="hero-actions-row">
          <BranchButton variant="primary" size="md" @click="scrollToResults">
            Browse prototypes
          </BranchButton>
          <BranchButton variant="secondary" size="md" @click="searchQuery = ''">
            Clear search
          </BranchButton>
        </div>

        <div class="hero-stats">
          <div class="hero-stat">
            <span class="hero-stat-label">Designers</span>
            <strong>{{ filteredDesigners.length }}</strong>
          </div>
          <div class="hero-stat">
            <span class="hero-stat-label">Prototypes</span>
            <strong>{{ filteredPrototypeCount }}</strong>
          </div>
          <div class="hero-stat">
            <span class="hero-stat-label">Mode</span>
            <strong>{{ searchQuery ? 'Filtered' : 'Live previews' }}</strong>
          </div>
        </div>
      </div>
    </header>

    <main id="results" class="main">
      <section
        v-for="designer in filteredDesigners"
        :key="designer.name"
        class="designer-section"
      >
        <div class="designer-header">
          <div class="avatar">{{ designer.initials }}</div>
          <div class="designer-meta">
            <h2 class="designer-name">{{ designer.name }}</h2>
            <p class="designer-sub">{{ designer.prototypes.length }} prototype{{ designer.prototypes.length === 1 ? '' : 's' }}</p>
          </div>
        </div>

        <div class="prototype-grid">
          <article
            v-for="proto in designer.prototypes"
            :key="proto.path"
            class="proto-card"
          >
            <NuxtLink :to="proto.path" class="proto-preview-link" :aria-label="`Open ${proto.name}`">
              <div class="proto-toolbar">
                <span class="proto-dot" />
                <span class="proto-dot" />
                <span class="proto-dot" />
                <span class="proto-route">{{ proto.path }}</span>
              </div>

              <div class="proto-preview-frame">
                <div class="proto-preview-scale">
                  <iframe
                    :src="proto.path"
                    :title="`${proto.name} preview`"
                    loading="lazy"
                    tabindex="-1"
                  />
                </div>
              </div>
            </NuxtLink>

            <div class="proto-info">
              <div class="proto-copy">
                <p class="proto-name">{{ proto.name }}</p>
                <p class="proto-meta">{{ proto.designer }} · {{ proto.date }}</p>
              </div>
              <BranchButton variant="secondary" size="sm" @click="router.push(proto.path)">
                Open
              </BranchButton>
            </div>
          </article>
        </div>
      </section>

      <section v-if="filteredDesigners.length === 0" class="empty-state">
        <span class="material-symbols-rounded empty-icon">search_off</span>
        <h2>No prototypes match that search.</h2>
        <p>Try a designer name, a route slug like `seller`, or clear the filter to browse everything again.</p>
        <BranchButton variant="secondary" size="md" @click="searchQuery = ''">
          Reset search
        </BranchButton>
      </section>
    </main>

    <footer class="footer">
      <div>
        <p class="footer-title">Adding a prototype</p>
        <ol class="footer-steps">
          <li>Create <code>/pages/[your-name]/[prototype-name].vue</code></li>
          <li>Run <code>npm run build</code> before pushing</li>
          <li>Push to GitHub and let Vercel deploy the route</li>
          <li>Reload this page to see the card appear automatically</li>
        </ol>
      </div>
    </footer>
  </div>
</template>

<script setup>
const theme = inject('theme')
const toggleTheme = inject('toggleTheme')
const router = useRouter()
const searchQuery = ref('')
const pageModules = import.meta.glob('./**/*.vue')

function toTitleCase(value) {
  return value
    .split('-')
    .filter(Boolean)
    .map(part => part.charAt(0).toUpperCase() + part.slice(1))
    .join(' ')
}

function formatPrototypeName(slug) {
  return toTitleCase(slug)
}

function formatDesignerName(slug) {
  if (slug === 'shared') return 'Shared'
  return toTitleCase(slug)
}

function scrollToResults() {
  document.getElementById('results')?.scrollIntoView({ behavior: 'smooth', block: 'start' })
}

const designers = computed(() => {
  const grouped = new Map()

  Object.keys(pageModules).forEach((filePath) => {
    const match = filePath.match(/^\.\/([^/]+)\/([^/]+)\.vue$/)
    if (!match) return

    const [, designerSlug, prototypeSlug] = match
    const routePath = `/${designerSlug}/${prototypeSlug}`
    const designerName = formatDesignerName(designerSlug)

    if (!grouped.has(designerSlug)) {
      grouped.set(designerSlug, {
        name: designerName,
        initials: designerName.charAt(0).toUpperCase(),
        prototypes: [],
      })
    }

    grouped.get(designerSlug).prototypes.push({
      name: formatPrototypeName(prototypeSlug),
      path: routePath,
      date: 'Mar 2026',
      designer: designerName,
    })
  })

  return Array.from(grouped.entries())
    .sort(([a], [b]) => {
      if (a === 'shared') return 1
      if (b === 'shared') return -1
      return a.localeCompare(b)
    })
    .map(([, designer]) => ({
      ...designer,
      prototypes: designer.prototypes.sort((a, b) => a.name.localeCompare(b.name)),
    }))
})

const filteredDesigners = computed(() => {
  const query = searchQuery.value.trim().toLowerCase()
  if (!query) return designers.value

  return designers.value
    .map(designer => ({
      ...designer,
      prototypes: designer.prototypes.filter((proto) => {
        const haystack = [designer.name, proto.name, proto.path].join(' ').toLowerCase()
        return haystack.includes(query)
      }),
    }))
    .filter(designer => designer.prototypes.length > 0)
})

const filteredPrototypeCount = computed(() =>
  filteredDesigners.value.reduce((total, designer) => total + designer.prototypes.length, 0)
)
</script>

<style scoped>
.playground {
  min-height: 100vh;
  background:
    radial-gradient(circle at top left, color-mix(in srgb, var(--section-cta) 32%, transparent), transparent 28%),
    var(--section-basement);
  color: var(--content-primary);
  padding-bottom: 72px;
}

.header {
  position: sticky;
  top: 0;
  z-index: 20;
  backdrop-filter: blur(20px);
  background: color-mix(in srgb, var(--section-basement) 88%, transparent);
  border-bottom: 1px solid var(--border);
}

.header-shell,
.hero,
.main,
.footer {
  width: min(1240px, calc(100% - 48px));
  margin: 0 auto;
}

.header-shell {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  padding: 20px 0;
}

.header-left,
.header-actions,
.designer-header,
.proto-info {
  display: flex;
  align-items: center;
}

.header-left,
.header-actions {
  gap: 14px;
}

.brand-mark,
.avatar {
  width: 44px;
  height: 44px;
  border-radius: 999px;
  display: grid;
  place-items: center;
  background: var(--section-cta);
  color: var(--content-always-black);
}

.header-eyebrow,
.hero-kicker,
.hero-stat-label,
.footer-title,
.designer-sub,
.proto-meta {
  margin: 0;
  color: var(--content-tertiary);
  font-size: 12px;
  font-weight: 600;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.header-title,
.hero-title,
.designer-name,
.proto-name,
.empty-state h2 {
  margin: 0;
}

.header-title {
  font-size: 18px;
  font-weight: 600;
}

.search-field {
  min-width: min(420px, 50vw);
  height: 44px;
  border: 1px solid var(--border);
  border-radius: 999px;
  background: var(--section-floor-1);
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 0 14px;
}

.search-field input {
  width: 100%;
  border: none;
  background: transparent;
  color: var(--content-primary);
  font: inherit;
}

.search-field input:focus {
  outline: none;
}

.search-icon,
.theme-toggle,
.empty-icon,
.material-symbols-rounded {
  color: var(--content-secondary);
}

.theme-toggle {
  width: 44px;
  height: 44px;
  border-radius: 12px;
  border: 1px solid var(--border);
  background: var(--section-floor-1);
  cursor: pointer;
}

.hero {
  display: grid;
  grid-template-columns: minmax(0, 1.1fr) auto;
  gap: 20px;
  padding: 8px 0 28px;
  align-items: end;
}

.hero-copy {
  display: grid;
  gap: 12px;
  max-width: 760px;
}

.hero-title {
  font-size: clamp(32px, 5vw, 56px);
  line-height: 0.96;
  letter-spacing: -0.04em;
}

.hero-sub {
  margin: 0;
  max-width: 680px;
  color: var(--content-secondary);
  font-size: 16px;
  line-height: 1.6;
}

.hero-actions-row,
.hero-stats {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
}

.hero-actions-row {
  justify-content: flex-end;
}

.hero-stats {
  grid-column: 1 / -1;
  padding-top: 6px;
}

.hero-stat {
  min-width: 144px;
  padding: 14px 16px;
  border-radius: 16px;
  border: 1px solid var(--border);
  background: color-mix(in srgb, var(--section-floor-1) 78%, transparent);
}

.hero-stat strong {
  display: block;
  margin-top: 6px;
  font-size: 20px;
  line-height: 1;
}

.main {
  display: grid;
  gap: 40px;
  padding-top: 40px;
}

.designer-section {
  display: grid;
  gap: 18px;
}

.designer-header {
  gap: 14px;
}

.designer-meta {
  display: grid;
  gap: 4px;
}

.designer-name {
  font-size: 22px;
  font-weight: 600;
}

.prototype-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  gap: 20px;
}

.proto-card {
  border: 1px solid var(--border);
  border-radius: 24px;
  overflow: hidden;
  background: var(--section-floor-1);
  box-shadow: 0 12px 32px color-mix(in srgb, var(--content-always-black) 6%, transparent);
}

.proto-preview-link {
  display: block;
  text-decoration: none;
}

.proto-toolbar {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 14px;
  border-bottom: 1px solid var(--border);
  background: color-mix(in srgb, var(--section-floor-2) 66%, transparent);
}

.proto-dot {
  width: 8px;
  height: 8px;
  border-radius: 999px;
  background: var(--content-quaternary);
}

.proto-route {
  margin-left: 4px;
  color: var(--content-tertiary);
  font-size: 12px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.proto-preview-frame {
  position: relative;
  height: 260px;
  overflow: hidden;
  background: var(--section-floor-2);
}

.proto-preview-scale {
  width: 1280px;
  height: 820px;
  transform: scale(0.28);
  transform-origin: top left;
  pointer-events: none;
}

.proto-preview-scale iframe {
  width: 1280px;
  height: 820px;
  border: 0;
  background: var(--section-basement);
}

.proto-info {
  justify-content: space-between;
  gap: 12px;
  padding: 16px;
}

.proto-copy {
  display: grid;
  gap: 4px;
}

.proto-name {
  font-size: 18px;
  font-weight: 600;
}

.empty-state {
  display: grid;
  justify-items: center;
  gap: 12px;
  padding: 56px 24px;
  border: 1px dashed var(--border);
  border-radius: 24px;
  background: var(--section-floor-1);
  text-align: center;
}

.empty-state p {
  max-width: 520px;
  margin: 0;
  color: var(--content-secondary);
  line-height: 1.6;
}

.empty-icon {
  font-size: 36px;
}

.footer {
  margin-top: 56px;
  padding-top: 28px;
  border-top: 1px solid var(--border);
}

.footer-steps {
  margin: 14px 0 0;
  padding-left: 20px;
  color: var(--content-secondary);
  display: grid;
  gap: 8px;
}

code {
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, monospace;
  font-size: 0.95em;
  padding: 0.12em 0.4em;
  border-radius: 6px;
  background: color-mix(in srgb, var(--section-floor-2) 72%, transparent);
}

@media (max-width: 980px) {
  .header-shell,
  .hero {
    grid-template-columns: 1fr;
  }

  .header-shell,
  .header-actions,
  .hero-actions-row {
    align-items: stretch;
  }

  .header-shell,
  .header-actions {
    flex-direction: column;
  }

  .search-field {
    min-width: 100%;
  }

  .hero-actions-row {
    justify-content: flex-start;
  }
}

@media (max-width: 640px) {
  .header-shell,
  .hero,
  .main,
  .footer {
    width: min(100% - 24px, 1240px);
  }

  .prototype-grid {
    grid-template-columns: 1fr;
  }

  .proto-preview-frame {
    height: 220px;
  }

  .proto-preview-scale {
    transform: scale(0.235);
  }

  .proto-info {
    align-items: flex-start;
    flex-direction: column;
  }
}
</style>
