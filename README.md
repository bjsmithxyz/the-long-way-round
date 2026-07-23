# The Long Way Round — trip tracker

A fast, static travel dashboard: an interactive world map of everywhere I've been and am headed, running trip stats, and a planning panel with **live seasonal weather + daylight** for each upcoming stop. Includes an in-browser editor that commits changes straight back to GitHub.

- `index.html` — the whole app (map, stats, timeline, editor). No build step, no framework.
- `trips.json` — the data. Every stop lives here; the site loads it and the editor writes to it.

## Data model (`trips.json`)
```json
{
  "meta": { "title": "...", "subtitle": "...", "eyebrow": "..." },
  "stops": [
    { "name":"Amsterdam", "country":"Netherlands", "cc":"NL",
      "lat":52.3676, "lon":4.9041,
      "arrive":"2026-07-25", "depart":"2026-08-01",
      "note":"optional", "tentative":true }
  ]
}
```
Past / current / upcoming is derived automatically from today's date — you never set it. Route lines follow the array order, so ordering matters. `tentative:true` shows an amber badge (used for the Europe legs after Paris — replace them with your real plan).

---

## Editing in the browser (with GitHub auth)
Click **✎ Edit trip** (top right). You can add, edit, reorder (↑ ↓), and delete stops for both travelled and planned legs, and use **⌖** to auto-fill coordinates + country code from a place name (Open-Meteo geocoding).

Three ways to save:
- **⤴ Save to GitHub** — commits `trips.json` directly. Needs a token (below).
- **Download trips.json** — no token; save the file and commit it yourself.
- **Preview on map** — apply edits locally to see them without saving.

### Creating the GitHub token (one time)
1. Go to **[github.com/settings/tokens](https://github.com/settings/tokens?type=beta)** → *Fine-grained tokens* → **Generate new token**.
2. **Repository access** → *Only select repositories* → `the-long-way-round`.
3. **Permissions** → *Repository permissions* → **Contents: Read and write**.
4. Generate, copy, paste into the editor's token box, hit **Save token**.

The token is stored **only in your browser** (localStorage) and sent directly to `api.github.com` — it never goes anywhere else. Clear it anytime with **Clear**. After a commit, GitHub Pages redeploys in ~1 minute.

> Security note: anyone with this token can write to that one repo, so keep it to a repo-scoped fine-grained token with only Contents access, and don't use the editor on a shared computer. If you'd rather never store a token, use **Download trips.json** and commit manually.

---

## Create the repo & push (first time)

This folder is already a git repo with your first commit (on branch `master`). Because it was prepared in a sandbox that can't delete files, there are a couple of stale `.git/*.lock` files — harmless, but git will complain until they're removed.

**Easiest — run the helper** (clears the locks, renames to `main`, creates the repo & pushes):
```bash
bash push.sh
```

Or do it by hand:

**If you have the GitHub CLI:**
```bash
rm -f .git/*.lock .git/refs/heads/*.lock   # clear sandbox leftovers
git branch -M main
```
Then:
```bash
gh repo create the-long-way-round --public --source=. --remote=origin --push
```

**Otherwise** — create an empty repo named `the-long-way-round` at [github.com/new](https://github.com/new) (no README), then:
```bash
git remote add origin https://github.com/bjsmithxyz/the-long-way-round.git
git branch -M main
git push -u origin main
```

### Turn on GitHub Pages
Repo → **Settings → Pages** → Source: **Deploy from a branch** → `main` / `/ (root)` → Save.
Live at **https://bjsmithxyz.github.io/the-long-way-round/** in ~1 minute.

---

## Optional: bjsmith.xyz subdomain via Netlify
1. Netlify → **Add new site → Import from GitHub** → pick `the-long-way-round` (auto-deploys on every push).
2. **Domain management → Add a custom domain** → `trips.bjsmith.xyz`.
3. At your DNS host add a `CNAME`: `trips` → `your-site.netlify.app`. HTTPS is automatic.

What Netlify could add later (site stays static today): a serverless function to proxy/cache Open-Meteo so weather never rate-limits, and GitHub **OAuth login** for the editor so you don't paste a token.

## Credits
Weather & daylight: [Open-Meteo](https://open-meteo.com) · Map: [Leaflet](https://leafletjs.com) + [CARTO](https://carto.com) tiles + OpenStreetMap.
