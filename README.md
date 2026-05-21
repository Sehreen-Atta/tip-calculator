# Splitwise — Tip Calculator

A single-screen tip calculator and bill splitter. Live updates as you type — no calculate button needed.

---

## How to run

**No build step required.** This is a single HTML file.

### Option 1 — open directly in browser

```bash
# Clone the repo
git clone https://github.com/Sehreen-Atta/tip-calculator.git
cd tip-calculator

# Open in browser (macOS)
open index.html

# Open in browser (Linux)
xdg-open index.html

# Open in browser (Windows)
start index.html
```

Or just double-click `index.html` in your file explorer.

### Option 2 — local dev server (recommended, avoids any CORS quirks)

```bash
# Using Python (no install needed if Python 3 is on your machine)
python3 -m http.server 3000
# then visit http://localhost:3000

# Using Node.js / npx
npx serve .
# then visit the URL it prints
```

### Requirements

- Any modern browser (Chrome, Firefox, Safari, Edge — all current versions)
- No npm install, no build tool, no environment variables

### Deployed URL

> https://comforting-daifuku-6532bd.netlify.app/ *(replace with your actual URL after deploying)*

---

## Deploy to Vercel (optional, 2 minutes)

1. Push this repo to GitHub
2. Go to [vercel.com](https://vercel.com) → New Project → Import your repo
3. Framework preset: **Other**
4. Root directory: `.` (the repo root)
5. Click Deploy — done. Vercel serves static HTML automatically.

---

## Features

- Live calculation as you type (no submit button)
- Preset tip buttons (10% / 15% / 20%) with active state
- Custom tip % input
- Inline validation — errors appear next to the offending field, not as alerts
- Rounding policy: round each person's share **up** to the nearest paisa so the group never underpays
- Reset button returns app to clean state
- Works on 360px mobile up to 1440px+ desktop
- Keyboard navigable, ARIA labels on all inputs and results
