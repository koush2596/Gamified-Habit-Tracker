# Summit habit tracker (installable web app)

## Put it online (GitHub Pages, free)
1. On github.com, create a new **public** repository, e.g. `summit`.
2. Add file → Upload files → drag in everything from this folder
   (index.html, manifest.webmanifest, sw.js, the icons folder). Commit.
3. Settings → Pages → Source: "Deploy from a branch" → Branch: `main`, folder `/ (root)` → Save.
4. After about a minute your app is live at `https://<your-username>.github.io/summit/`.

## Install on iPhone
Open the link in Safari → Share → Add to Home Screen → Add.

## Install on Android
Open the link in Chrome → ⋮ menu → Install app (or Add to Home screen).

## Notes
- Each phone keeps its own data. Anyone who installs from the link gets their own tracker.
- Use Habits → Backup → Save backup now and then.
- To update the app: upload the new index.html to the repo, then bump `CACHE` in sw.js (e.g. summit-v2).
