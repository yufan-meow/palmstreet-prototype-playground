<template>
  <div class="root">

    <!-- Proto chrome bar -->
    <div class="proto-bar">
      <NuxtLink to="/" class="back-link">
        <span class="material-symbols-rounded" style="font-size:20px">arrow_back</span>
        <span>Playground</span>
      </NuxtLink>
      <span class="proto-label">Bulk Edit</span>
      <button class="proto-theme-toggle" @click="toggleTheme()">
        <span class="material-symbols-rounded" style="font-size:18px">{{ theme === 'dark' ? 'light_mode' : 'dark_mode' }}</span>
      </button>
    </div>

    <!-- ═══════════════════════════════════════════
         LISTINGS VIEW
    ═══════════════════════════════════════════ -->
    <template v-if="!showBulkEdit">

      <!-- Page header -->
      <div class="page-header">
        <h1 class="page-heading">Listings</h1>
        <div class="header-actions">
          <button class="btn secondary">Import by CSV</button>
          <button class="btn primary">Create Listing</button>
        </div>
      </div>

      <!-- Tab bar -->
      <div class="tab-bar">
        <button
          v-for="tab in tabs"
          :key="tab.id"
          class="tab"
          :class="{ active: activeTab === tab.id }"
          @click="activeTab = tab.id"
        >
          {{ tab.label }}
        </button>
      </div>

      <!-- Search + filter toolbar -->
      <div class="toolbar">
        <div class="search-box">
          <span class="material-symbols-rounded icon-sm">search</span>
          <input placeholder="Search" class="search-input" />
        </div>
        <button class="btn secondary sm">
          Add Filters
          <span class="material-symbols-rounded icon-sm">add</span>
        </button>
        <button class="sort-pill">
          Sort: Newest first
          <span class="material-symbols-rounded icon-sm">expand_more</span>
        </button>
      </div>

      <!-- Bulk action bar — visible when any item selected -->
      <div v-if="selectedCount > 0" class="bulk-bar">
        <label class="bulk-check-label">
          <input
            type="checkbox"
            class="cb"
            :checked="allSelected"
            :indeterminate.prop="!allSelected && selectedCount > 0"
            @change="toggleAll"
          />
          <span class="bulk-count-label">{{ selectedCount }} selected</span>
        </label>
        <button class="link-btn" @click="deselectAll">
          Deselect all {{ selectedCount }}
        </button>
        <div class="bulk-actions">
          <button class="btn secondary sm" @click="openBulkEdit">Bulk edit</button>
          <button class="btn secondary sm">Mark as sold</button>
          <button class="btn secondary sm">Make Public</button>
          <button class="btn destructive sm">Delete</button>
        </div>
      </div>

      <!-- Listings table -->
      <div class="listings-list">
        <div
          v-for="listing in listings"
          :key="listing.id"
          class="listing-row"
          :class="{ 'row-selected': listing.selected }"
        >
          <input type="checkbox" class="cb" v-model="listing.selected" />
          <div class="listing-thumb">
            <span class="material-symbols-rounded thumb-icon">local_florist</span>
          </div>
          <span class="listing-name">{{ listing.title }}</span>
          <span class="badge available">Available</span>
          <span class="meta">{{ listing.stock }} in stock</span>
          <span class="meta">{{ listing.variants }} Variants</span>
          <span class="price">${{ listing.price }}</span>
          <span class="type-pill">{{ listing.type }}</span>
          <button class="row-action-btn">
            <span class="material-symbols-rounded icon-sm">more_horiz</span>
          </button>
        </div>
      </div>

      <!-- Table footer -->
      <div class="table-footer">
        <span class="footer-count">{{ listings.length }} results</span>
        <div class="pagination">
          <span class="page-label">1/1 page</span>
          <button class="btn secondary sm" disabled>Previous</button>
          <button class="btn secondary sm" disabled>Next page</button>
        </div>
      </div>

    </template>

    <!-- ═══════════════════════════════════════════
         BULK EDIT VIEW
    ═══════════════════════════════════════════ -->
    <template v-else>

      <!-- Sticky sub-header — sits below proto-bar -->
      <div class="be-header">
        <div class="be-header-left">
          <button class="icon-btn" @click="closeBulkEdit">
            <span class="material-symbols-rounded">close</span>
          </button>
          <span class="be-title">Bulk Edit</span>
          <span class="be-dot">•</span>
          <span class="be-count">{{ selectedListings.length }} Listings</span>
        </div>
        <div class="be-header-right">
          <button class="btn secondary" @click="closeBulkEdit">Cancel</button>
          <button
            class="btn primary"
            :disabled="!hasChanges"
            @click="saveChanges"
          >
            Save
          </button>
        </div>
      </div>

      <!-- Scrollable spreadsheet -->
      <div class="be-scroll">
        <table class="be-table">
          <thead>
            <tr>
              <!-- Each th: resizable via drag handle, width driven by colWidths -->
              <th :style="{ width: colWidths.check + 'px' }">
                <div class="resize-handle" @mousedown.prevent="startResize($event, 'check')"></div>
              </th>
              <th :style="{ width: colWidths.image + 'px' }">
                Image
                <div class="resize-handle" @mousedown.prevent="startResize($event, 'image')"></div>
              </th>
              <th :style="{ width: colWidths.title + 'px' }">
                Title
                <div class="resize-handle" @mousedown.prevent="startResize($event, 'title')"></div>
              </th>
              <th :style="{ width: colWidths.stock + 'px' }">
                Total stock
                <div class="resize-handle" @mousedown.prevent="startResize($event, 'stock')"></div>
              </th>
              <th :style="{ width: colWidths.price + 'px' }">
                Price
                <div class="resize-handle" @mousedown.prevent="startResize($event, 'price')"></div>
              </th>
              <th :style="{ width: colWidths.type + 'px' }">
                Type
                <div class="resize-handle" @mousedown.prevent="startResize($event, 'type')"></div>
              </th>
              <th :style="{ width: colWidths.vis + 'px' }">
                Visibility
                <div class="resize-handle" @mousedown.prevent="startResize($event, 'vis')"></div>
              </th>
              <th :style="{ width: colWidths.ship + 'px' }">
                Shipping
                <div class="resize-handle" @mousedown.prevent="startResize($event, 'ship')"></div>
              </th>
              <th :style="{ width: colWidths.sku + 'px' }">
                SKU
                <div class="resize-handle" @mousedown.prevent="startResize($event, 'sku')"></div>
              </th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(listing, idx) in selectedListings"
              :key="listing.id"
              class="be-row"
              :class="{ 'be-row-editing': editingRow === idx }"
            >
              <!-- Checkbox -->
              <td class="td-cell" :style="{ width: colWidths.check + 'px' }">
                <input type="checkbox" class="cb" />
              </td>

              <!-- Image -->
              <td class="td-cell" :style="{ width: colWidths.image + 'px' }">
                <div class="be-thumb">
                  <span class="material-symbols-rounded thumb-icon-sm">local_florist</span>
                </div>
              </td>

              <!-- Title — click to edit, overflow arrow when > 20 chars -->
              <td
                class="td-cell"
                :class="{ 'cell-editing': editingRow === idx }"
                :style="{ width: colWidths.title + 'px' }"
              >
                <input
                  v-if="editingRow === idx"
                  ref="titleInputEl"
                  v-model="listing.title"
                  class="be-title-input"
                  @blur="editingRow = null"
                  @keyup.enter="editingRow = null"
                  @keyup.escape="editingRow = null"
                  @input="hasChanges = true"
                />
                <div
                  v-else
                  class="be-title-text"
                  @click="startEditTitle(idx)"
                >
                  <span class="title-inner">{{ listing.title }}</span>
                  <span
                    v-if="listing.title.length > 20"
                    class="material-symbols-rounded title-overflow-arrow"
                  >chevron_right</span>
                </div>
              </td>

              <!-- Total stock -->
              <td class="td-cell" :style="{ width: colWidths.stock + 'px' }">
                <span class="cell-text">{{ listing.stock }}</span>
              </td>

              <!-- Price -->
              <td class="td-cell" :style="{ width: colWidths.price + 'px' }">
                <input
                  v-model="listing.price"
                  type="number"
                  min="0"
                  step="0.01"
                  class="be-price-input"
                  @input="hasChanges = true"
                />
              </td>

              <!-- Type dropdown -->
              <td class="td-cell" :style="{ width: colWidths.type + 'px' }">
                <div class="be-select-wrap">
                  <select v-model="listing.type" class="be-select" @change="hasChanges = true">
                    <option>Auction</option>
                    <option>Buy Now</option>
                  </select>
                  <span class="material-symbols-rounded be-chevron">expand_more</span>
                </div>
              </td>

              <!-- Visibility dropdown -->
              <td class="td-cell" :style="{ width: colWidths.vis + 'px' }">
                <div class="be-select-wrap">
                  <select v-model="listing.visibility" class="be-select" @change="hasChanges = true">
                    <option>Private</option>
                    <option>Public</option>
                  </select>
                  <span class="material-symbols-rounded be-chevron">expand_more</span>
                </div>
              </td>

              <!-- Shipping dropdown -->
              <td class="td-cell" :style="{ width: colWidths.ship + 'px' }">
                <div class="be-select-wrap">
                  <select v-model="listing.shipping" class="be-select" @change="hasChanges = true">
                    <option>Test shipping ($10 + $2)</option>
                    <option>Standard ($5 + $1)</option>
                    <option>Free shipping</option>
                  </select>
                  <span class="material-symbols-rounded be-chevron">expand_more</span>
                </div>
              </td>

              <!-- SKU -->
              <td class="td-cell" :style="{ width: colWidths.sku + 'px' }">
                <input
                  v-model="listing.sku"
                  class="be-sku-input"
                  placeholder="Input SKU here"
                  @input="hasChanges = true"
                />
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Save success toast -->
      <div v-if="showToast" class="toast">
        <span class="material-symbols-rounded toast-icon">check_circle</span>
        <span class="toast-text">{{ selectedListings.length }} listings updated</span>
      </div>

    </template>
  </div>
</template>

<script setup>
import { ref, computed, nextTick } from 'vue'

// ─── Theme ────────────────────────────────────────────
const theme = inject('theme')
const toggleTheme = inject('toggleTheme')

// ─── State ───────────────────────────────────────────
const showBulkEdit = ref(false)
const hasChanges = ref(false)
const editingRow = ref(null)
const showToast = ref(false)
const titleInputEl = ref(null)

const tabs = [
  { id: 'public', label: 'Public' },
  { id: 'private', label: 'Private' },
  { id: 'deleted', label: 'Recently Deleted' },
]
const activeTab = ref('private')

// ─── Column widths (resizable) ────────────────────────
const colWidths = ref({
  check: 40,
  image: 72,
  title: 220,
  stock: 110,
  price: 100,
  type: 148,
  vis: 148,
  ship: 230,
  sku: 180,
})

// ─── Column resize logic ──────────────────────────────
const activeResize = ref(null)

function startResize(e, colKey) {
  activeResize.value = { colKey, startX: e.clientX, startWidth: colWidths.value[colKey] }

  function onMove(e) {
    if (!activeResize.value) return
    const delta = e.clientX - activeResize.value.startX
    colWidths.value[activeResize.value.colKey] = Math.max(48, activeResize.value.startWidth + delta)
  }

  function onUp() {
    activeResize.value = null
    window.removeEventListener('mousemove', onMove)
    window.removeEventListener('mouseup', onUp)
  }

  window.addEventListener('mousemove', onMove)
  window.addEventListener('mouseup', onUp)
}

// ─── Mock data ────────────────────────────────────────
const listings = ref([
  { id: 1, title: 'monstera albo variegata', stock: 1, price: '25.00', type: 'Auction',  visibility: 'Private', shipping: 'Test shipping ($10 + $2)', sku: '', selected: true,  variants: 1 },
  { id: 2, title: 'test6',                   stock: 1, price: '2.00',  type: 'Buy Now',  visibility: 'Private', shipping: 'Test shipping ($10 + $2)', sku: '', selected: true,  variants: 1 },
  { id: 3, title: 'test 3',                  stock: 1, price: '1.00',  type: 'Auction',  visibility: 'Private', shipping: 'Test shipping ($10 + $2)', sku: '', selected: true,  variants: 1 },
  { id: 4, title: 'test 2',                  stock: 1, price: '1.00',  type: 'Buy Now',  visibility: 'Private', shipping: 'Test shipping ($10 + $2)', sku: '', selected: true,  variants: 1 },
  { id: 5, title: 'test',                    stock: 1, price: '1.00',  type: 'Buy Now',  visibility: 'Private', shipping: 'Test shipping ($10 + $2)', sku: '', selected: true,  variants: 1 },
  { id: 6, title: 'marble — large specimen', stock: 1, price: '2.00',  type: 'Auction',  visibility: 'Private', shipping: 'Test shipping ($10 + $2)', sku: '', selected: true,  variants: 1 },
  { id: 7, title: 'hhhhhh',                  stock: 1, price: '1.00',  type: 'Buy Now',  visibility: 'Private', shipping: 'Test shipping ($10 + $2)', sku: '', selected: false, variants: 1 },
  { id: 8, title: 'watch',                   stock: 1, price: '5.00',  type: 'Auction',  visibility: 'Private', shipping: 'Test shipping ($10 + $2)', sku: '', selected: false, variants: 1 },
])

// ─── Computed ─────────────────────────────────────────
const selectedListings = computed(() => listings.value.filter(l => l.selected))
const selectedCount = computed(() => selectedListings.value.length)
const allSelected = computed(
  () => listings.value.length > 0 && listings.value.every(l => l.selected)
)

// ─── Methods ──────────────────────────────────────────
function toggleAll(e) {
  listings.value.forEach(l => (l.selected = e.target.checked))
}

function deselectAll() {
  listings.value.forEach(l => (l.selected = false))
}

function openBulkEdit() {
  hasChanges.value = false
  showBulkEdit.value = true
}

function closeBulkEdit() {
  showBulkEdit.value = false
  editingRow.value = null
}

async function startEditTitle(idx) {
  editingRow.value = idx
  hasChanges.value = true
  await nextTick()
  const el = Array.isArray(titleInputEl.value) ? titleInputEl.value[0] : titleInputEl.value
  el?.focus()
  el?.select()
}

function saveChanges() {
  editingRow.value = null
  showToast.value = true
  hasChanges.value = false
  setTimeout(() => {
    showToast.value = false
    showBulkEdit.value = false
  }, 1800)
}
</script>

<style scoped>
/* ─────────────────────────────────────────────────────
   ROOT
───────────────────────────────────────────────────── */
.root {
  font-family: 'Gabarito', sans-serif;
  background: var(--section-basement);
  min-height: 100vh;
  color: var(--content-primary);
  position: relative;
}

/* ─────────────────────────────────────────────────────
   PROTO CHROME BAR  — explicit height so children can offset against it
───────────────────────────────────────────────────── */
.proto-bar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 24px;
  height: 48px;              /* explicit — be-header and thead reference this */
  border-bottom: 1px solid var(--border);
  background: var(--section-floor-1);
  position: sticky;
  top: 0;
  z-index: 30;
  box-sizing: border-box;
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
.proto-theme-toggle {
  width: 32px;
  height: 32px;
  border-radius: 8px;
  border: 1px solid var(--border);
  background: var(--section-basement);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--content-primary);
}

/* ─────────────────────────────────────────────────────
   SHARED BUTTON SYSTEM
───────────────────────────────────────────────────── */
.btn {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  height: 40px;
  padding: 0 16px;
  border-radius: 8px;
  font-family: 'Gabarito', sans-serif;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  border: none;
  transition: filter 0.1s;
  white-space: nowrap;
}
.btn.sm { height: 32px; font-size: 14px; padding: 0 12px; }
.btn.primary { background: var(--section-cta); color: var(--content-always-black); }
.btn.primary:disabled { opacity: 0.4; cursor: not-allowed; }
.btn.secondary { background: var(--section-floor-1); color: var(--content-primary); border: 1px solid var(--border); }
.btn.secondary:disabled { opacity: 0.4; cursor: not-allowed; }
.btn.destructive { background: var(--section-destructive); color: var(--content-destructive); border: none; }
.btn:not(:disabled):hover { filter: brightness(0.96); }

.link-btn {
  background: none;
  border: none;
  padding: 0;
  font-family: 'Gabarito', sans-serif;
  font-size: 14px;
  font-weight: 500;
  color: var(--content-interactive);
  cursor: pointer;
  white-space: nowrap;
}
.icon-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  border: none;
  background: none;
  cursor: pointer;
  border-radius: 8px;
  color: var(--content-primary);
  flex-shrink: 0;
}
.icon-btn:hover { background: var(--section-interactive); }
.icon-sm { font-size: 18px !important; line-height: 1; }
.cb { width: 16px; height: 16px; flex-shrink: 0; accent-color: var(--section-cta); cursor: pointer; }

/* ─────────────────────────────────────────────────────
   LISTINGS — PAGE HEADER
───────────────────────────────────────────────────── */
.page-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 24px 32px;
  border-bottom: 1px solid var(--border);
}
.page-heading { font-size: 28px; font-weight: 600; color: var(--content-primary); }
.header-actions { display: flex; gap: 8px; }

/* ─── TABS ─── */
.tab-bar { display: flex; padding: 0 32px; border-bottom: 1px solid var(--border); }
.tab {
  padding: 14px 16px;
  background: none;
  border: none;
  border-bottom: 2px solid transparent;
  margin-bottom: -1px;
  font-family: 'Gabarito', sans-serif;
  font-size: 16px;
  font-weight: 500;
  color: var(--content-secondary);
  cursor: pointer;
}
.tab.active { color: var(--content-interactive); border-bottom-color: var(--content-interactive); }

/* ─── TOOLBAR ─── */
.toolbar { display: flex; align-items: center; gap: 12px; padding: 16px 32px; }
.search-box {
  display: flex;
  align-items: center;
  gap: 8px;
  height: 40px;
  padding: 0 12px;
  background: var(--section-floor-1);
  border: 1px solid var(--border);
  border-radius: 8px;
  width: 220px;
  color: var(--content-tertiary);
}
.search-input { flex: 1; background: none; border: none; outline: none; font-family: 'Gabarito', sans-serif; font-size: 16px; color: var(--content-primary); }
.search-input::placeholder { color: var(--content-tertiary); }
.sort-pill {
  display: flex;
  align-items: center;
  gap: 4px;
  margin-left: auto;
  height: 40px;
  padding: 0 12px;
  background: var(--section-floor-1);
  border: 1px solid var(--border);
  border-radius: 8px;
  font-family: 'Gabarito', sans-serif;
  font-size: 14px;
  color: var(--content-secondary);
  cursor: pointer;
}

/* ─── BULK ACTION BAR ─── */
.bulk-bar { display: flex; align-items: center; gap: 16px; padding: 10px 32px; border-bottom: 1px solid var(--border); background: var(--section-basement); }
.bulk-check-label { display: flex; align-items: center; gap: 8px; cursor: pointer; }
.bulk-count-label { font-size: 14px; font-weight: 500; color: var(--content-primary); }
.bulk-actions { display: flex; gap: 8px; margin-left: auto; }

/* ─── LISTINGS TABLE ROWS ─── */
.listings-list { padding: 0 32px; }
.listing-row {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 12px 0;
  border-bottom: 1px solid var(--border);
  transition: background 0.1s;
}
.listing-row:hover { background: var(--section-interactive); margin: 0 -32px; padding: 12px 32px; }
.listing-thumb { width: 48px; height: 48px; border-radius: 8px; background: var(--section-floor-2); display: flex; align-items: center; justify-content: center; flex-shrink: 0; color: var(--content-tertiary); }
.thumb-icon { font-size: 24px; }
.listing-name { flex: 1; min-width: 100px; font-size: 16px; color: var(--content-primary); }
.badge { display: inline-flex; align-items: center; padding: 2px 8px; border-radius: 4px; font-size: 12px; font-weight: 600; }
.badge.available { background: var(--section-success); color: var(--content-success); }
.meta { font-size: 14px; color: var(--content-secondary); min-width: 72px; white-space: nowrap; }
.price { font-size: 16px; font-weight: 500; color: var(--content-primary); min-width: 56px; }
.type-pill { font-size: 12px; color: var(--content-secondary); border: 1px solid var(--border); padding: 2px 8px; border-radius: 4px; white-space: nowrap; }
.row-action-btn { background: none; border: none; cursor: pointer; color: var(--content-tertiary); padding: 4px; border-radius: 4px; display: flex; align-items: center; }
.row-action-btn:hover { background: var(--section-floor-2); }

/* ─── TABLE FOOTER ─── */
.table-footer { display: flex; align-items: center; justify-content: space-between; padding: 16px 32px; border-top: 1px solid var(--border); }
.footer-count { font-size: 14px; color: var(--content-secondary); }
.pagination { display: flex; align-items: center; gap: 8px; }
.page-label { font-size: 14px; color: var(--content-secondary); margin-right: 8px; }

/* ─────────────────────────────────────────────────────
   BULK EDIT — STICKY HEADER
   top: 48px  =  proto-bar height
───────────────────────────────────────────────────── */
.be-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 24px;
  height: 64px;
  border-bottom: 1px solid var(--border);
  background: var(--section-basement);
  position: sticky;
  top: 48px;                 /* proto-bar = 48px */
  z-index: 20;
  box-sizing: border-box;
}
.be-header-left { display: flex; align-items: center; gap: 8px; }
.be-title { font-size: 18px; font-weight: 600; color: var(--content-primary); }
.be-dot { font-size: 18px; color: var(--content-tertiary); }
.be-count { font-size: 18px; font-weight: 400; color: var(--content-secondary); }
.be-header-right { display: flex; gap: 8px; }

/* ─────────────────────────────────────────────────────
   BULK EDIT — SPREADSHEET TABLE
───────────────────────────────────────────────────── */
.be-scroll {
  overflow-x: auto;
  overflow-y: visible;
}
.be-table {
  border-collapse: collapse;
  font-family: 'Gabarito', sans-serif;
  font-size: 14px;
  table-layout: fixed;       /* respect explicit widths */
}

/* ─── Column header row
   top: 112px  =  proto-bar (48px) + be-header (64px)
───────────────────────────────────────────────────── */
.be-table thead th {
  background: var(--section-floor-1);
  color: var(--content-secondary);
  font-size: 12px;
  font-weight: 600;
  text-align: left;
  padding: 10px 12px;
  border-bottom: 1px solid var(--border);
  border-right: 1px solid var(--border);
  white-space: nowrap;
  overflow: hidden;
  position: sticky;
  top: 112px;                /* 48 + 64 */
  z-index: 10;
  /* needed so the resize handle is clipped cleanly */
  position: sticky;
}
.be-table thead th:last-child { border-right: none; }

/* ─── Resize handle ─── */
.resize-handle {
  position: absolute;
  right: 0;
  top: 0;
  bottom: 0;
  width: 5px;
  cursor: col-resize;
  z-index: 1;
  background: transparent;
  transition: background 0.1s;
}
.resize-handle:hover,
.resize-handle:active {
  background: var(--border-strong);
}

/* Body cells */
.td-cell {
  padding: 8px 12px;
  border-bottom: 1px solid var(--border);
  border-right: 1px solid var(--border);
  vertical-align: middle;
  background: var(--section-basement);
  overflow: hidden;
}
.td-cell:last-child { border-right: none; }

/* Row editing highlight */
.be-row-editing .td-cell { background: var(--section-floor-1); }

/* Thumbnail */
.be-thumb { width: 48px; height: 48px; border-radius: 8px; background: var(--section-floor-2); display: flex; align-items: center; justify-content: center; color: var(--content-tertiary); }
.thumb-icon-sm { font-size: 20px; }

/* ─── Title cell ─── */
.be-title-text {
  display: flex;
  align-items: center;
  gap: 4px;
  cursor: text;
  padding: 4px;
  border-radius: 4px;
  border: 1px solid transparent;
  overflow: hidden;
}
.be-title-text:hover { border-color: var(--border); background: var(--section-floor-1); }

.title-inner {
  flex: 1;
  min-width: 0;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  color: var(--content-primary);
}

/* Arrow indicator — appears when title > 20 chars */
.title-overflow-arrow {
  font-size: 16px !important;
  color: var(--content-tertiary);
  flex-shrink: 0;
  line-height: 1;
}

.be-title-input {
  width: 100%;
  padding: 4px 8px;
  background: var(--section-floor-1);
  border: 1.5px solid var(--border-strong);
  border-radius: 4px;
  font-family: 'Gabarito', sans-serif;
  font-size: 14px;
  color: var(--content-primary);
  outline: none;
  box-sizing: border-box;
}

/* Stock */
.cell-text { color: var(--content-primary); font-size: 14px; }

/* Price input */
.be-price-input {
  width: 100%;
  background: none;
  border: none;
  border-bottom: 1.5px solid transparent;
  font-family: 'Gabarito', sans-serif;
  font-size: 14px;
  color: var(--content-primary);
  outline: none;
  padding: 2px 0;
  box-sizing: border-box;
}
.be-price-input:focus { border-bottom-color: var(--border-strong); }

/* Select cells */
.be-select-wrap { display: flex; align-items: center; position: relative; overflow: hidden; }
.be-select {
  appearance: none;
  -webkit-appearance: none;
  background: none;
  border: none;
  font-family: 'Gabarito', sans-serif;
  font-size: 14px;
  color: var(--content-primary);
  cursor: pointer;
  outline: none;
  flex: 1;
  min-width: 0;
  padding-right: 24px;
}
.be-chevron { font-size: 18px !important; color: var(--content-secondary); pointer-events: none; position: absolute; right: 0; }

/* SKU input */
.be-sku-input {
  width: 100%;
  background: none;
  border: none;
  border-bottom: 1.5px solid transparent;
  font-family: 'Gabarito', sans-serif;
  font-size: 14px;
  color: var(--content-primary);
  outline: none;
  padding: 2px 0;
  box-sizing: border-box;
}
.be-sku-input::placeholder { color: var(--content-quaternary); }
.be-sku-input:focus { border-bottom-color: var(--border); }

/* ─────────────────────────────────────────────────────
   TOAST — always inverse tokens
───────────────────────────────────────────────────── */
.toast {
  position: fixed;
  top: 24px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  align-items: center;
  gap: 8px;
  height: 48px;
  padding: 0 16px;
  background: var(--section-floor-1-inverse);
  border-radius: 12px;
  z-index: 100;
  animation: toast-in 0.2s ease;
}
.toast-icon { font-size: 24px; color: var(--content-success); }
.toast-text { font-family: 'Gabarito', sans-serif; font-size: 14px; font-weight: 500; color: var(--content-primary-inverse); white-space: nowrap; }
@keyframes toast-in {
  from { opacity: 0; transform: translateX(-50%) translateY(-8px); }
  to   { opacity: 1; transform: translateX(-50%) translateY(0); }
}
</style>
