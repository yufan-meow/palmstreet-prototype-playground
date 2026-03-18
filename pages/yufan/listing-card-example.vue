<template>
  <div class="page">
    <!-- Prototype chrome -->
    <div class="proto-bar">
      <NuxtLink to="/" class="back-link">
        <span class="icon" style="font-size:20px">arrow_back</span>
        <span>Playground</span>
      </NuxtLink>
      <span class="proto-label">Listing Card — Example</span>
      <button class="theme-toggle" @click="toggleTheme()">
        <span class="icon" style="font-size:18px">{{ theme === 'dark' ? 'light_mode' : 'dark_mode' }}</span>
      </button>
    </div>

    <!-- Prototype content -->
    <main class="canvas">
      <div class="listing-card">
        <div class="card-image">
          <div class="badge">Live</div>
        </div>
        <div class="card-body">
          <div class="card-top">
            <p class="card-title">Monstera Deliciosa — Large</p>
            <p class="card-seller">@greenleaf_studio</p>
          </div>
          <div class="card-bottom">
            <p class="card-price">$48.00</p>
            <button class="btn-primary">Add to cart</button>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
const theme = inject('theme')
const toggleTheme = inject('toggleTheme')
</script>

<style scoped>
.page {
  min-height: 100vh;
  background: var(--section-basement);
}

/* Prototype chrome bar — appears in all prototypes */
.proto-bar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 24px;
  border-bottom: 1px solid var(--border);
  background: var(--section-floor-1);
  position: sticky;
  top: 0;
  z-index: 10;
}

.back-link {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 14px;
  color: var(--content-interactive);
  text-decoration: none;
}

.proto-label {
  font-size: 14px;
  font-weight: 500;
  color: var(--content-secondary);
}

.theme-toggle {
  width: 32px;
  height: 32px;
  border-radius: 8px;
  border: 1px solid var(--border);
  background: var(--section-basement);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Canvas where prototype lives */
.canvas {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 48px 24px;
  min-height: calc(100vh - 57px);
}

/* Example listing card */
.listing-card {
  width: 280px;
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid var(--border);
  background: var(--section-floor-1);
}

.card-image {
  height: 200px;
  background: var(--section-floor-2);
  position: relative;
  display: flex;
  align-items: flex-start;
  padding: 12px;
}

.badge {
  background: #FF3B30;
  color: var(--content-always-white);
  font-size: 12px;
  font-weight: 600;
  padding: 4px 8px;
  border-radius: 9999px;
}

.card-body {
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.card-top {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.card-title {
  font-size: 16px;
  font-weight: 500;
  color: var(--content-primary);
}

.card-seller {
  font-size: 12px;
  color: var(--content-tertiary);
}

.card-bottom {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.card-price {
  font-size: 18px;
  font-weight: 600;
  color: var(--content-primary);
}

.btn-primary {
  height: 40px;
  padding: 0 16px;
  border-radius: 9999px;
  border: none;
  background: var(--section-cta);
  color: var(--content-always-black);
  font-family: 'Gabarito', sans-serif;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: opacity 0.15s;
}

.btn-primary:hover {
  opacity: 0.88;
}
</style>
