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

The app has two modes, switched with the pill control at the top: **Workout** and **Tasks**.

**Workout**
- **Today** — Sunday-anchored week strip at the top; bar height is that day's completion.
  Tap any day to view or fill it in. Tap a row to mark it done. Rows with number logging
  have an input on the side.
- **Library** — add, edit, delete exercises and categories. Every exercise has a category,
  an optional target area/dose, the weekdays it appears on, and an optional tracked number.
- **Progress** — a scrollable day-by-day feed: each day that had something scheduled gets a
  card listing its exercises with a ✔/✘. Tap a day to open it on Today. No weekly percentages,
  no personal-best tracking here — just what happened, per day.

**Tasks**
- **Tasks** — a plain to-do list. Pin a task and it repeats every day (its checkbox resets
  each morning); leave it unpinned and it's a one-off for today only, then it rolls off the
  active list. Tap a task to rename, re-pin, or delete it; tap its checkbox to mark it done.
- **Progress** — the same day-by-day feed as Workout Progress, but for tasks.

## Daily housekeeping

The week strip on Today re-anchors to the current Sunday automatically. Day-by-day logs
(workout and tasks) are kept for the last 84 days and pruned beyond that; one-off tasks past
their day are pruned the same way. This runs on load and whenever the app returns to the
foreground — no user action needed.

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
- Days of history kept: `HISTORY_DAYS` near the top of the script.
- Units for tracked numbers: the array in `openExSheet` (`["reps","seconds",...]`).
- After editing, bump `CACHE` in `sw.js` (e.g. `bodylog-v3`) or the old version keeps serving.
