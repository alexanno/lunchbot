# 🍱 Lunchbot — Lunch Topic of the Week

A minimal static site that shows a new discussion topic every week, drawn from:
- **[Ig Nobel Prizes](https://improbable.com/ig/winners/)** — real research that first makes you laugh, then makes you think
- **[Spurious Correlations](https://www.tylervigen.com/spurious/random)** — Tyler Vigen's statistically significant nonsense (now with AI-generated fake academic papers)

Hosted on **GitHub Pages**, refreshed every **Monday at 08:00 UTC** by a GitHub Actions workflow.

---

## Setup (3 steps)

### 1. Push to GitHub

```bash
git init
git add .
git commit -m "feat: initial lunchbot"
git remote add origin https://github.com/YOUR_USERNAME/lunchbot.git
git push -u origin main
```

### 2. Enable GitHub Pages

In your repo → **Settings → Pages**:

- **Source:** `Deploy from a branch`
- **Branch:** `main` / `/ (root)`
- Save → your site will be live at `https://YOUR_USERNAME.github.io/lunchbot/`

### 3. Trigger the first update

Go to **Actions → Update Lunch Topic → Run workflow**.  
This fetches a fresh topic, commits `topic.json`, and the page updates automatically.

---

## How it works

```
Monday 08:00 UTC
       │
       ▼
GitHub Actions (.github/workflows/weekly-update.yml)
       │
       ▼
scripts/fetch_topic.py
  ├── tries tylervigen.com/spurious/random  (AI-generated papers)
  ├── tries improbable.com/ig/winners/      (Ig Nobel winners)
  └── falls back to built-in curated list   (18 hand-picked topics)
       │
       ▼
topic.json  (committed to main)
       │
       ▼
index.html reads topic.json via fetch()  →  GitHub Pages serves it
```

The workflow commits only if `topic.json` actually changed (so duplicate runs are safe).

---

## Running locally

```bash
uv run --with requests --with beautifulsoup4 python scripts/fetch_topic.py  # writes topic.json
python -m http.server 8080           # serve the page (any HTTP server works)
# open http://localhost:8080
```

> `index.html` uses `fetch('topic.json')` which requires an HTTP server — it won't work
> from a `file://` URL directly.

---

## Customising

| What | Where |
|---|---|
| Add/remove fallback topics | `FALLBACK_TOPICS` list in `scripts/fetch_topic.py` |
| Change schedule | `cron:` line in `.github/workflows/weekly-update.yml` |
| Add a new source | Write a new `fetch_*()` function and add it to `main()` |
| Change colours | `PALETTE` map in `index.html` |
