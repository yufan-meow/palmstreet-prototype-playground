<template>
  <div class="playground">
    <!-- Header -->
    <header class="header">
      <div class="header-left">
        <span class="header-logo">🌴</span>
        <div>
          <h1 class="header-title">Prototype Playground</h1>
          <p class="header-sub">Palmstreet Design Team</p>
        </div>
      </div>
      <button class="theme-toggle" @click="toggleTheme()">
        <span class="icon" style="font-size:20px">{{ theme === 'dark' ? 'light_mode' : 'dark_mode' }}</span>
      </button>
    </header>

    <!-- Designer sections -->
    <main class="main">
      <section v-for="designer in designers" :key="designer.name" class="designer-section">
        <div class="designer-header">
          <div class="avatar">{{ designer.initials }}</div>
          <h2 class="designer-name">{{ designer.name }}</h2>
        </div>

        <div class="prototype-grid">
          <NuxtLink
            v-for="proto in designer.prototypes"
            :key="proto.path"
            :to="proto.path"
            class="proto-card"
          >
            <div class="proto-thumb">
              <span class="icon" style="font-size:32px; color: var(--content-tertiary)">draft</span>
            </div>
            <div class="proto-info">
              <p class="proto-name">{{ proto.name }}</p>
              <p class="proto-date">{{ proto.date }}</p>
            </div>
          </NuxtLink>

          <!-- Empty state -->
          <div v-if="designer.prototypes.length === 0" class="empty-state">
            <span class="icon" style="color: var(--content-tertiary)">add_circle</span>
            <p>No prototypes yet</p>
          </div>
        </div>
      </section>
    </main>

    <!-- How to add -->
    <footer class="footer">
      <p class="footer-title">Adding a prototype</p>
      <ol class="footer-steps">
        <li>Create <code>/pages/[your-name]/[prototype-name].vue</code></li>
        <li>Push your branch to GitHub</li>
        <li>Vercel generates a preview URL automatically</li>
        <li>Share the URL in Slack or Linear</li>
      </ol>
    </footer>
  </div>
</template>

<script setup>
const theme = inject('theme')
const toggleTheme = inject('toggleTheme')

// Add your name and prototypes here as you build them
const designers = ref([
  {
    name: 'Yufan',
    initials: 'Y',
    prototypes: [
      { name: 'Example — Listing Card', path: '/yufan/listing-card-example', date: 'Mar 2026' },
      { name: 'Bulk Edit', path: '/yufan/bulk-edit-prototype', date: 'Mar 2026' }
    ]
  },
  {
    name: 'Shared',
    initials: 'S',
    prototypes: []
  }
])
</script>

<style scoped>
.playground {
  min-height: 100vh;
  background: var(--section-basement);
  padding: 0 0 80px;
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 24px 32px;
  border-bottom: 1px solid var(--border);
  position: sticky;
  top: 0;
  background: var(--section-basement);
  z-index: 10;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 12px;
}

.header-logo {
  font-size: 28px;
}

.header-title {
  font-size: 16px;
  font-weight: 600;
  color: var(--content-primary);
}

.header-sub {
  font-size: 12px;
  color: var(--content-tertiary);
  margin-top: 2px;
}

.theme-toggle {
  width: 40px;
  height: 40px;
  border-radius: 8px;
  border: 1px solid var(--border);
  background: var(--section-floor-1);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.15s;
}

.theme-toggle:hover {
  background: var(--section-floor-2);
}

.main {
  max-width: 960px;
  margin: 0 auto;
  padding: 48px 32px 0;
  display: flex;
  flex-direction: column;
  gap: 48px;
}

.designer-section {}

.designer-header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 16px;
}

.avatar {
  width: 32px;
  height: 32px;
  border-radius: 9999px;
  background: var(--section-cta);
  color: var(--content-always-black);
  font-size: 14px;
  font-weight: 600;
  display: flex;
  align-items: center;
  justify-content: center;
}

.designer-name {
  font-size: 18px;
  font-weight: 600;
  color: var(--content-primary);
}

.prototype-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 16px;
}

.proto-card {
  border: 1px solid var(--border);
  border-radius: 12px;
  overflow: hidden;
  background: var(--section-floor-1);
  text-decoration: none;
  transition: border-color 0.15s, transform 0.15s;
}

.proto-card:hover {
  border-color: var(--border-strong);
  transform: translateY(-2px);
}

.proto-thumb {
  height: 120px;
  background: var(--section-floor-2);
  display: flex;
  align-items: center;
  justify-content: center;
}

.proto-info {
  padding: 12px;
}

.proto-name {
  font-size: 14px;
  font-weight: 500;
  color: var(--content-primary);
}

.proto-date {
  font-size: 12px;
  color: var(--content-tertiary);
  margin-top: 4px;
}

.empty-state {
  border: 1px dashed var(--border);
  border-radius: 12px;
  height: 160px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
  color: var(--content-tertiary);
  font-size: 14px;
}

.footer {
  max-width: 960px;
  margin: 64px auto 0;
  padding: 24px 32px;
  border-top: 1px solid var(--border);
}

.footer-title {
  font-size: 12px;
  font-weight: 600;
  color: var(--content-tertiary);
  text-transform: uppercase;
  letter-spacing: 0.08em;
  margin-bottom: 12px;
}

.footer-steps {
  display: flex;
  flex-direction: column;
  gap: 6px;
  padding-left: 20px;
}

.footer-steps li {
  font-size: 14px;
  color: var(--content-secondary);
}

code {
  font-family: monospace;
  font-size: 12px;
  background: var(--section-floor-2);
  padding: 2px 6px;
  border-radius: 4px;
  color: var(--content-primary);
}
</style>
