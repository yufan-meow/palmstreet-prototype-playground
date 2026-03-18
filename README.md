# Palmstreet Prototype Playground

A shared environment for the Palmstreet design team to build and share interactive prototypes. Every branch auto-deploys to a Vercel preview URL — engineers review prototypes by opening a link, no local setup needed.

---

## Quick Start (local dev)

```bash
npm install
npm run dev
```

Open `http://localhost:3000`

---

## Adding a New Prototype

1. Create a new file: `/pages/[your-name]/[prototype-name].vue`
2. Copy the chrome bar from `/pages/yufan/listing-card-example.vue` (the top bar with back button + theme toggle)
3. Build your prototype in the `<canvas>` section
4. Use only tokens from `/styles/tokens.css` — no raw hex values
5. Push your branch to GitHub
6. Vercel automatically generates a preview URL
7. Share the URL in Slack or Linear

---

## File Structure

```
/pages
  index.vue                      ← Playground home (lists all prototypes)
  /yufan/                        ← Yufan's prototypes
  /[designer-name]/              ← Other designers' prototypes
  /shared/                       ← Shared reference components
/styles
  tokens.css                     ← ALL Branch DS semantic tokens
  base.css                       ← Reset, Gabarito font, body defaults
/components                      ← Reusable prototype components
CLAUDE.md                        ← AI context (Claude Code reads this automatically)
```

---

## Using Claude to Build Prototypes (Option B workflow)

For designers without a local dev setup, use Claude.ai:

1. Open the Palmstreet Claude Project
2. Describe what you want to build (or paste a Figma description)
3. Claude generates a `.vue` file using correct Branch DS tokens
4. Add the file to GitHub via the web UI (github.com → your folder → Add file)
5. Vercel deploys automatically

---

## Design System Reference

All tokens documented in `/CLAUDE.md` and `/styles/tokens.css`.

Full design system: `github.com/yufan-meow/palmstreet-branch-design-system`
