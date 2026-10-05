# Summit – Gamified Habit Tracker

## Overview

Summit is a gamified habit tracker that turns daily routines into a mountain climb. Every completed habit earns coins, streaks earn bonus coins, and lifetime progress moves you up a trail from **Level 1 (Trailhead)** to **Level 30 (Summit)**.

It is an installable Progressive Web App (PWA): it runs in the browser, can be added to the home screen like a native app, works offline, and needs no account, server or sign-up. Each user's data stays on their own device.

## Live Demo

👉 **https://koush2596.github.io/Gamified-Habit-Tracker/**

Open the link on your phone and add it to your home screen:
- **iPhone (Safari):** Share → Add to Home Screen → Add
- **Android (Chrome):** ⋮ menu → Install app (or Add to Home screen)

## Features

**Habits and progress**
- Daily check-ins for any number of habits, each with its own emoji and coin value (5–20 coins)
- Streak bonus: +2 coins per consecutive day, up to +10
- Perfect-day bonus: +20 coins when every habit is completed
- 30 levels across seven zones (Trailhead, Forest line, Alpine meadow, The ridge, Glacier, Summit push, Summit), shown as an animated mountain trail with camp flags every 5 levels
- Log or correct yesterday's habits
- 7-day overview grid

**Notes**
- Add a short note to any completed check-in (e.g. "30 pages", "Leg day")
- Recent notes are collected on the Stats page as a lightweight journal

**Stats and insights**
- Lifetime totals: check-ins, coins earned, perfect days and longest streak
- Monthly calendar heatmap shaded by daily completion
- Per-habit completion rate for the last 30 days and best streak
- Weekly recap (this week / last week): completion %, coins, perfect days, strongest habit, habit needing attention, and week-over-week comparison. A recap banner appears at the start of each week.

**Achievements**
- 18 badges, including First step, Perfect day, 7/21/50/100-day streaks, Perfect week, 100 and 500 check-ins, level milestones, Journaler and Get it done
- Pop-up notification when a badge is unlocked

**Shop**
- Spend coins on **streak shields**, which protect streaks after a missed day
- Create custom real-life rewards with your own coin prices
- Spending coins never lowers your level

**To-do list**
- A simple to-do list for one-off tasks, with done/undo, delete and "clear done"

**App experience**
- Installable as a home-screen app with a custom icon
- Full-screen standalone mode
- Works offline
- Light and dark mode (follows the system by default, with a manual toggle)
- Mobile-first layout with safe-area support for notched phones

## Tech Stack

| Layer | Technology |
|---|---|
| UI | HTML5, CSS3 (custom properties, CSS Grid, Flexbox, `color-mix`) |
| Logic | Vanilla JavaScript (ES2017+), no frameworks or build step |
| Graphics | Inline SVG for the mountain and trail animation |
| Storage | Browser `localStorage` |
| Offline | Service Worker with the Cache API |
| Installability | Web App Manifest and Apple touch icons |
| Fonts | Google Fonts (Bricolage Grotesque, Figtree) |
| Hosting | GitHub Pages |

## How It Works

1. **Check in:** tap a habit to mark it done for today (or yesterday).
2. **Earn coins:** each check-in earns the habit's base coins plus a streak bonus. Completing all habits in a day adds +20.
3. **Climb:** lifetime coins earned determine your level. The XP needed for level *n* is `25 × n × (n − 1)`, so early levels come quickly and higher levels take sustained consistency.
4. **Protect streaks:** if you miss a day, use a streak shield from the Shop to keep your streaks alive.
5. **Reward yourself:** spend coins on the rewards you've defined. Your balance goes down, but your level never does.
6. **Reflect:** review the Stats page, weekly recap and achievements to see patterns over time.

All derived values (coins, streaks, levels, achievements, stats) are recomputed from the raw check-in history each time, so there is a single source of truth and no counters can drift out of sync.

## Architecture

```
Gamified-Habit-Tracker/
├── index.html             # Single-page app: markup, styles and logic
├── manifest.webmanifest   # PWA metadata (name, icons, display mode, colors)
├── sw.js                  # Service worker for offline support and caching
├── icons/
│   ├── apple-touch-icon.png
│   ├── icon-192.png
│   ├── icon-512.png
│   ├── icon-maskable-512.png
│   └── favicon-32.png
└── README.md
```

**Design approach**
- **Single state object:** all user data lives in one JSON object (`habits`, `log`, `notes`, `shieldDays`, `rewards`, `purchases`, `todos`).
- **Event-sourced style computation:** the `log` is a map of `date → [habitIds]`. A single `compute()` pass walks the history day by day to derive coins, streaks, levels, perfect days and per-day completion.
- **Change wrapper:** every user action runs through a `change()` function that snapshots the computed state before and after, then persists, re-renders and shows level-up or achievement notifications automatically.
- **Declarative achievements:** each badge is defined as `{ id, emoji, name, description, test(computed) }`, so new achievements can be added in one line.
- **Rendering:** plain DOM rendering through a small `el()` helper. User-entered text is always inserted as text, never as HTML.
- **Service worker strategy:** network-first for the app page (so updates arrive) with a cache fallback offline, and cache-first for icons and fonts.

## Data Persistence

- All data is stored locally in the browser's `localStorage` under the key `summit-habits-v1`.
- **No server, no account, no tracking:** nothing leaves the device.
- **One tracker per user:** anyone who installs the app gets their own independent tracker.
- **No cross-device sync:** each device keeps its own data unless you restore a backup onto it.
- The app requests persistent storage (`navigator.storage.persist()`) to reduce the chance of the browser clearing data.

**Things that can erase data**
- Deleting the home-screen app on iOS
- Clearing website data in browser settings
- Switching to a new phone without restoring a backup

On iOS, the installed home-screen app and the same URL opened in a Safari tab use **separate storage**. Always open Summit from the home-screen icon.

## Backup and Restore

Backups are managed from **Habits → Backup**.

- **Save backup:** exports all data as a JSON file (`summit-backup-YYYY-MM-DD.json`). On phones this opens the share sheet, so you can save it to Files, iCloud Drive or Google Drive.
- **Restore backup:** imports a backup file and replaces the current data, after a confirmation prompt.
- The date of the last backup is shown so you know when it's time for a new one.

Restoring a backup is also how you move your progress to a new phone.

## AI-Assisted Development

This project was built in collaboration with **Claude (Anthropic)**, used as an AI pair programmer:

- **Concept to prototype:** started from the idea of a gamified habit tracker and turned it into an original design built around a mountain-climb metaphor.
- **Iterative feature development:** features were added in small, conversation-driven iterations: core tracking, then quick-add, then stats, achievements, notes, weekly recap and the to-do list.
- **Architecture and refactoring:** moved from a hosted prototype to a standalone, installable PWA with offline support, local persistence and backup/restore.
- **Testing:** the app was tested manually in the browser and on real phones (iPhone and Android), covering check-ins, coin and streak calculation, notes, to-dos, achievements, backup/restore and offline use. There is no automated test suite in the repository yet.
- **Human in the loop:** product decisions, requirements, icon artwork, deployment and real-device testing were done by the developer.

## Running Locally

The app has no dependencies and no build step. Because of the service worker, it must be served over HTTP rather than opened as a `file://` URL.

```bash
git clone https://github.com/koush2596/Gamified-Habit-Tracker.git
cd Gamified-Habit-Tracker

# Option 1: Python
python3 -m http.server 8080

# Option 2: Node
npx serve .
```

Then open **http://localhost:8080** in your browser.

**Deploying updates to GitHub Pages**
1. Commit and push changes to `main`.
2. Bump the cache version in `sw.js` (e.g. `summit-v1` → `summit-v2`) so installed apps pick up the new version.
3. Users receive the update the next time they open the app.

## Future Improvements

- **Cloud sync:** optional account (e.g. Firebase) for syncing across devices
- **Friends and challenges:** shared leaderboards and partner streaks
- **Reminders:** push notifications to log habits at a chosen time
- **Flexible schedules:** weekly targets (e.g. "Workout 4× a week") and specific weekdays
- **Habit categories:** grouping (Body, Mind, Growth) with color coding
- **Coins for to-dos:** optional small rewards for completed tasks
- **Data export:** CSV export for analysis in spreadsheets
- **Automatic backups:** scheduled backup reminders or export to cloud storage
- **Automated tests:** unit tests for the coin, streak and level logic, and end-to-end tests for the main flows
- **Localization:** multi-language support (e.g. German)
- **Accessibility audit:** full screen-reader and keyboard testing
