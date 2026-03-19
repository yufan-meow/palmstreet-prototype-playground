<script setup>
const categoryCards = [
  {
    name: 'Reptiles',
    detail: 'Geckos, snakes, feeders, habitat bundles, and live-show-ready drops.',
    metric: 'Top category',
    accent: '42% repeat buyers',
    icon: 'pets',
  },
  {
    name: 'Rare Finds',
    detail: 'Limited collectibles, accessories, and niche inventory that benefits from live demos.',
    metric: 'High intent',
    accent: 'Avg. cart +28%',
    icon: 'diamond',
  },
  {
    name: 'New Arrivals',
    detail: 'Fresh weekly drops with timed launch windows and fast claim flows.',
    metric: 'Fast launch',
    accent: 'Go live in 1 day',
    icon: 'bolt',
  },
]

const reptilePlans = [
  {
    tier: 'Starter Shed',
    price: '$29',
    cadence: '/month',
    description: 'For first-time reptile sellers getting their catalog and first lives ready.',
    features: ['Up to 30 active listings', 'Basic live show tools', 'Shipping label discounts'],
    recommended: false,
  },
  {
    tier: 'Scale Up',
    price: '$99',
    cadence: '/month',
    description: 'For active reptile shops that need more visibility, moderation, and repeat buyer flow.',
    features: ['Unlimited reptile listings', 'Priority discovery placement', 'Team seat for moderators'],
    recommended: true,
  },
  {
    tier: 'Breeder Pro',
    price: '$249',
    cadence: '/month',
    description: 'For larger breeders running recurring launches, waitlists, and premium buyer relationships.',
    features: ['VIP launch support', 'Advanced audience segments', 'Dedicated seller success partner'],
    recommended: false,
  },
]

const coinBenefits = [
  'Reward buyers with coins after live claims or order milestones.',
  'Run coin-only drops to turn loyal fans into repeat purchasers.',
  'Bundle coins with reptile plans to accelerate first-month retention.',
]

const sellingPoints = [
  {
    title: 'Category-first discovery',
    body: 'Surface the right inventory to collectors who already shop by species, rarity, and price band.',
  },
  {
    title: 'Built for live selling',
    body: 'Manage drops, comments, claims, and post-show fulfillment without stitching together extra tools.',
  },
  {
    title: 'Coins that compound demand',
    body: 'Use platform incentives to keep buyers coming back between launches and restocks.',
  },
  {
    title: 'Seller operations that scale',
    body: 'Labels, payouts, catalog edits, and customer follow-up stay in one workflow.',
  },
]
</script>

<template>
  <main class="seller-page">
    <section class="hero-card">
      <div class="hero-top">
        <div class="brand-pill">
          <span class="material-symbols-rounded" aria-hidden="true">storefront</span>
          <span>Palmstreet Seller Launch</span>
        </div>
        <a class="text-link" href="#signup">Seller sign up</a>
      </div>

      <div class="hero-grid">
        <div class="hero-copy">
          <p class="eyebrow">Seller landing page</p>
          <h1>
            Turn reptile inventory into
            <span>repeat buyers, repeat shows, and repeat coins.</span>
          </h1>
          <p class="hero-text">
            Launch with category-led discovery, pricing plans built for reptile sellers, and coin-driven retention that keeps your audience buying between drops.
          </p>

          <div class="hero-actions">
            <a id="signup" class="button button-primary" href="https://www.palmstreet.app/sell" target="_blank" rel="noreferrer">
              Sign up to sell
            </a>
            <a class="button button-secondary" href="#plans">View reptile plans</a>
          </div>

          <div class="hero-stats">
            <article class="stat-card">
              <span class="stat-label">Category fit</span>
              <strong>Reptiles first</strong>
              <p>Merchandising and launches organized around what collectors actually browse.</p>
            </article>
            <article class="stat-card">
              <span class="stat-label">Coins</span>
              <strong>Built-in rewards</strong>
              <p>Drive more second and third purchases without manual coupon systems.</p>
            </article>
          </div>
        </div>

        <aside class="hero-panel">
          <div class="panel-kicker">
            <span class="material-symbols-rounded" aria-hidden="true">trending_up</span>
            Seller value stack
          </div>
          <ul class="stack-list">
            <li>Category merchandising that matches collector behavior</li>
            <li>Reptile-specific plans for shops, breeders, and launch-heavy sellers</li>
            <li>Coins that create urgency and retention across every drop</li>
          </ul>

          <div class="signal-card">
            <div>
              <p class="signal-label">Live launch readiness</p>
              <strong>Go from catalog to stream in one workflow.</strong>
            </div>
            <span class="material-symbols-rounded" aria-hidden="true">videocam</span>
          </div>
        </aside>
      </div>
    </section>

    <section class="content-grid">
      <section class="surface-card">
        <div class="section-heading">
          <p class="eyebrow">Category</p>
          <h2>Merchandise by what buyers hunt for.</h2>
        </div>

        <div class="category-grid">
          <article v-for="card in categoryCards" :key="card.name" class="category-card">
            <div class="category-icon">
              <span class="material-symbols-rounded" aria-hidden="true">{{ card.icon }}</span>
            </div>
            <p class="category-metric">{{ card.metric }}</p>
            <h3>{{ card.name }}</h3>
            <p>{{ card.detail }}</p>
            <strong>{{ card.accent }}</strong>
          </article>
        </div>
      </section>

      <section id="plans" class="surface-card">
        <div class="section-heading">
          <p class="eyebrow">Reptile plans</p>
          <h2>Choose the seller plan that matches your inventory velocity.</h2>
        </div>

        <div class="plans-grid">
          <article
            v-for="plan in reptilePlans"
            :key="plan.tier"
            class="plan-card"
            :class="{ 'plan-card--featured': plan.recommended }"
          >
            <p class="plan-badge">{{ plan.recommended ? 'Most popular' : 'Flexible start' }}</p>
            <h3>{{ plan.tier }}</h3>
            <div class="price-row">
              <strong>{{ plan.price }}</strong>
              <span>{{ plan.cadence }}</span>
            </div>
            <p class="plan-copy">{{ plan.description }}</p>
            <ul>
              <li v-for="feature in plan.features" :key="feature">{{ feature }}</li>
            </ul>
          </article>
        </div>
      </section>

      <section class="surface-card surface-card--accent">
        <div class="section-heading">
          <p class="eyebrow">Coins</p>
          <h2>Keep demand warm after the show ends.</h2>
        </div>

        <div class="coins-layout">
          <div class="coin-orb">
            <span class="material-symbols-rounded" aria-hidden="true">monetization_on</span>
          </div>
          <ul class="coin-list">
            <li v-for="item in coinBenefits" :key="item">{{ item }}</li>
          </ul>
        </div>
      </section>

      <section class="surface-card">
        <div class="section-heading">
          <p class="eyebrow">Selling points</p>
          <h2>What makes this seller flow worth switching for.</h2>
        </div>

        <div class="selling-grid">
          <article v-for="point in sellingPoints" :key="point.title" class="selling-card">
            <h3>{{ point.title }}</h3>
            <p>{{ point.body }}</p>
          </article>
        </div>
      </section>

      <section class="cta-card">
        <div>
          <p class="eyebrow">Start selling</p>
          <h2>Open your storefront, list your reptiles, and launch with coins from day one.</h2>
        </div>
        <a class="button button-primary" href="https://www.palmstreet.app/sell" target="_blank" rel="noreferrer">
          Create seller account
        </a>
      </section>
    </section>
  </main>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Gabarito:wght@400;500;600;700&family=Material+Symbols+Rounded:opsz,wght,FILL,GRAD@20..48,400,1,0');

:global(body) {
  margin: 0;
  min-height: 100vh;
  background:
    radial-gradient(circle at top left, rgba(185, 236, 81, 0.4), transparent 22%),
    radial-gradient(circle at bottom right, rgba(34, 105, 236, 0.12), transparent 28%),
    linear-gradient(180deg, #fcfcfb 0%, #f0f1ed 100%);
  color: var(--content-primary);
  font-family: Gabarito, sans-serif;
}

:global(*) {
  box-sizing: border-box;
}

.seller-page {
  --content-primary: #1b1b1b;
  --content-secondary: #575757;
  --content-tertiary: #8c8c8c;
  --content-on-dark: #ffffff;
  --surface-base: rgba(255, 255, 255, 0.84);
  --surface-muted: #f7f7f5;
  --surface-strong: #171717;
  --surface-cta: #b9ec51;
  --surface-accent: #eef7d3;
  --border-soft: rgba(27, 27, 27, 0.08);
  --border-strong: #d9d9d4;
  --shadow-card: 0 24px 80px rgba(27, 27, 27, 0.08);
  width: min(1240px, 100%);
  margin: 0 auto;
  padding: 32px 20px 56px;
}

.hero-card,
.surface-card,
.cta-card {
  background: var(--surface-base);
  border: 1px solid rgba(255, 255, 255, 0.72);
  box-shadow: var(--shadow-card);
  backdrop-filter: blur(18px);
}

.hero-card {
  border-radius: 36px;
  padding: 28px;
  overflow: hidden;
}

.hero-top,
.hero-actions,
.price-row,
.cta-card,
.signal-card {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
}

.brand-pill,
.panel-kicker,
.plan-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 10px 14px;
  border-radius: 999px;
  font-size: 12px;
  font-weight: 600;
  letter-spacing: 0.06em;
  text-transform: uppercase;
}

.brand-pill {
  background: rgba(255, 255, 255, 0.86);
  color: var(--content-primary);
}

.text-link {
  color: #2269ec;
  font-size: 14px;
  font-weight: 500;
  text-decoration: none;
}

.hero-grid {
  display: grid;
  grid-template-columns: minmax(0, 1.2fr) minmax(300px, 360px);
  gap: 24px;
  margin-top: 24px;
}

.eyebrow {
  margin: 0 0 10px;
  color: var(--content-tertiary);
  font-size: 12px;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

h1,
h2,
h3,
p,
ul {
  margin: 0;
}

h1 {
  max-width: 700px;
  font-size: clamp(42px, 6vw, 72px);
  line-height: 0.96;
  letter-spacing: -0.04em;
}

h1 span {
  display: block;
  color: var(--content-secondary);
  font-weight: 500;
}

.hero-text {
  max-width: 560px;
  margin-top: 20px;
  color: var(--content-secondary);
  font-size: 17px;
  line-height: 1.65;
}

.hero-actions {
  justify-content: flex-start;
  flex-wrap: wrap;
  margin-top: 28px;
}

.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-height: 46px;
  padding: 0 18px;
  border-radius: 14px;
  border: 1px solid transparent;
  font-size: 15px;
  font-weight: 600;
  text-decoration: none;
}

.button-primary {
  background: var(--surface-cta);
  color: #000000;
}

.button-secondary {
  background: var(--surface-muted);
  border-color: var(--border-strong);
  color: var(--content-primary);
}

.hero-stats,
.category-grid,
.plans-grid,
.selling-grid {
  display: grid;
  gap: 16px;
}

.hero-stats {
  grid-template-columns: repeat(2, minmax(0, 1fr));
  margin-top: 28px;
}

.stat-card,
.category-card,
.plan-card,
.selling-card {
  border-radius: 26px;
  border: 1px solid var(--border-soft);
  background: rgba(255, 255, 255, 0.8);
  padding: 20px;
}

.stat-label,
.category-metric {
  color: var(--content-tertiary);
  font-size: 12px;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.stat-card strong,
.category-card strong {
  display: block;
  margin-top: 12px;
  font-size: 24px;
  line-height: 1.05;
}

.stat-card p,
.category-card p,
.plan-copy,
.selling-card p,
.coin-list li {
  color: var(--content-secondary);
  font-size: 15px;
  line-height: 1.6;
}

.hero-panel {
  border-radius: 32px;
  background: var(--surface-strong);
  color: var(--content-on-dark);
  padding: 24px;
  display: grid;
  gap: 20px;
}

.panel-kicker {
  width: fit-content;
  background: rgba(255, 255, 255, 0.08);
}

.stack-list,
.plan-card ul,
.coin-list {
  padding-left: 18px;
  display: grid;
  gap: 12px;
}

.signal-card {
  border-radius: 24px;
  background: rgba(255, 255, 255, 0.08);
  padding: 18px;
}

.signal-label {
  margin-bottom: 6px;
  color: rgba(255, 255, 255, 0.72);
  font-size: 12px;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.content-grid {
  display: grid;
  gap: 22px;
  margin-top: 22px;
}

.surface-card {
  border-radius: 32px;
  padding: 28px;
}

.surface-card--accent {
  background:
    radial-gradient(circle at top right, rgba(185, 236, 81, 0.36), transparent 28%),
    rgba(255, 255, 255, 0.9);
}

.section-heading {
  display: grid;
  gap: 8px;
  margin-bottom: 20px;
}

.section-heading h2,
.cta-card h2 {
  max-width: 760px;
  font-size: clamp(28px, 4vw, 42px);
  line-height: 1.02;
  letter-spacing: -0.03em;
}

.category-grid,
.plans-grid {
  grid-template-columns: repeat(3, minmax(0, 1fr));
}

.category-card,
.selling-card {
  display: grid;
  gap: 12px;
}

.category-icon {
  width: 44px;
  height: 44px;
  border-radius: 14px;
  display: grid;
  place-items: center;
  background: var(--surface-cta);
  color: #000000;
}

.plan-card {
  display: grid;
  gap: 14px;
}

.plan-card--featured {
  background: linear-gradient(180deg, rgba(185, 236, 81, 0.24) 0%, rgba(255, 255, 255, 0.88) 100%);
  border-color: rgba(185, 236, 81, 0.72);
}

.plan-badge {
  width: fit-content;
  background: var(--surface-muted);
  color: var(--content-secondary);
}

.plan-card--featured .plan-badge {
  background: rgba(185, 236, 81, 0.6);
  color: var(--content-primary);
}

.price-row {
  justify-content: flex-start;
  gap: 8px;
}

.price-row strong {
  font-size: 40px;
  line-height: 0.95;
  letter-spacing: -0.04em;
}

.coins-layout {
  display: grid;
  grid-template-columns: 160px minmax(0, 1fr);
  gap: 24px;
  align-items: center;
}

.coin-orb {
  width: 160px;
  height: 160px;
  border-radius: 50%;
  display: grid;
  place-items: center;
  background:
    radial-gradient(circle at top, rgba(255, 255, 255, 0.75), rgba(185, 236, 81, 0.95) 55%, rgba(146, 189, 53, 1) 100%);
  color: #000000;
  box-shadow: inset 0 8px 18px rgba(255, 255, 255, 0.48);
}

.coin-orb .material-symbols-rounded {
  font-size: 56px;
}

.selling-grid {
  grid-template-columns: repeat(2, minmax(0, 1fr));
}

.cta-card {
  border-radius: 32px;
  padding: 28px;
  background: #171717;
  color: var(--content-on-dark);
  flex-wrap: wrap;
}

.material-symbols-rounded {
  font-family: 'Material Symbols Rounded', sans-serif;
  font-variation-settings: 'FILL' 1, 'wght' 400, 'GRAD' 0, 'opsz' 24;
  line-height: 1;
}

@media (max-width: 980px) {
  .hero-grid,
  .category-grid,
  .plans-grid,
  .coins-layout,
  .selling-grid,
  .hero-stats {
    grid-template-columns: 1fr;
  }

  .coin-orb {
    margin: 0 auto;
  }
}

@media (max-width: 640px) {
  .seller-page {
    padding: 14px 12px 32px;
  }

  .hero-card,
  .surface-card,
  .cta-card {
    border-radius: 24px;
    padding: 20px;
  }

  h1 {
    font-size: 42px;
  }

  .section-heading h2,
  .cta-card h2 {
    font-size: 30px;
  }
}
</style>
