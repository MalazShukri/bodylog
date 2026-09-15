# Body Log

Single-page PWA for tracking body work. No build step, no framework, no backend, no cost.
All data lives in `localStorage` on the device.

## Files

```
index.html               the whole app (HTML + CSS + JS)
manifest.json            PWA manifest
sw.js                    service worker (offline)
icon-192.png             app icon
icon-512.png             app icon
icon-512-maskable.png    Android adaptive icon
```

## Deploy to GitHub Pages

```bash
git init
git add .
git commit -m "body log"
git branch -M main
git remote add origin git@github.com:<you>/bodylog.git
git push -u origin main
```

Then: repo → **Settings → Pages → Source: Deploy from a branch → main / (root)** → Save.
URL is `https://<you>.github.io/bodylog/` after ~1 minute.

Works on any static host (Netlify, Vercel, Cloudflare Pages) — just drop the folder in.
HTTPS is required for the service worker; GitHub Pages gives it by default.

## Install on the phone

- **Android / Chrome:** open the URL → menu → *Add to Home screen* → *Install*.
- **iOS / Safari:** open the URL → Share → *Add to Home Screen*.

After that it launches full-screen with no browser chrome and works with no signal.

## How it works

- **Today** — Sunday-anchored week strip at the top; bar height is that day's completion.
  Tap any day to view or fill it in. Tap a row to mark it done. Rows with number logging
  have an input on the side.
- **Library** — add, edit, delete exercises and categories. Every exercise has a category,
  an optional type/target/dose, the weekdays it appears on, and an optional tracked number.
- **Progress** — weekly completion for the last 12 weeks, this week broken down by category,
  and all-time personal bests.

## Weekly reset

Every Sunday the daily ticks clear. Before clearing, the finished week is summarised into
history (done/total overall and per category) and kept for 12 weeks. Personal bests are
never cleared. Rollover runs on load and whenever the app returns to the foreground.

## Bilingual text

Every field that holds your text uses `dir="auto"`, so an Arabic exercise name renders
right-to-left and an English one left-to-right — in the same list, no setting to flip.
The interface itself stays English. Font is IBM Plex Sans Arabic, which covers both scripts
in one family; if it can't load, the system Arabic stack is used.

## Backups

Progress → **Data** → *Export backup*. This is the only copy protection you have — clearing
browser data or resetting the phone wipes `localStorage`. Export monthly. The same JSON is
the migration path if this ever moves into a real database.

## Changing things

- Reset day: `sundayOf()` in `index.html` does `x.getDate() - x.getDay()`. For Monday use
  `(x.getDay() + 6) % 7`; for Saturday use `(x.getDay() + 1) % 7`.
- Weeks of history: `HISTORY_WEEKS` near the top of the script.
- Units for tracked numbers: the array in `openExSheet` (`["reps","seconds",...]`).
- After editing, bump `CACHE` in `sw.js` (e.g. `bodylog-v2`) or the old version keeps serving.
