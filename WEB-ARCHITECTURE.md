# motofuel.app — Web Architecture

## Hosting
- **Platform:** Cloudflare Pages (migrated from Netlify, May 2026)
- **Repo:** github.com/thereykjavikrider/motofuel-website
- **Deploy:** Auto-deploys on every `git push` to main
- **Domain:** motofuel.app (primary) + www.motofuel.app (redirects)
- **DNS:** Namecheap → Cloudflare nameservers
- **SSL:** Cloudflare-managed, auto-renews
- **Email:** support@motofuel.app via Cloudflare Email Routing → Gmail

## File Structure
motofuel-website/
  ├── index.html           — Landing page (motofuel.app)
  ├── support.html         — Support page (motofuel.app/support)
  ├── privacy.html         — Privacy policy (motofuel.app/privacy)
  ├── dashboard.html       — My MotoFuel Dashboard
  ├── dashboard-guide.html — How to use the dashboard
  ├── demoData/            — zzr_demo.json (the ?demo=1 dataset)
  ├── WEB-ARCHITECTURE.md  — This file
  ├── WEBSITE-LOG.md       — Claim-sync / operational change log
  └── .gitignore           — Excludes .DS_Store

## Branches
- **main** — what deploys to motofuel.app.
- **launch-app-store-badge** — holds the App Store badges and links (Apple ID `6762513433`:
  `https://apps.apple.com/app/id6762513433`). The nav "Get it on the App Store" link and the
  hero / CTA "Download on the App Store" badges live here, not on main. Everything committed to
  main is merged into this branch straight away so it stays current.
- **Release day:** wait until the App Store link actually works, then
  `git checkout main && git merge launch-app-store-badge && git push`.
  Until then main shows a non-link "Coming soon to the App Store" label instead of the badge.

## Pages

### index.html — Landing Page
Pure HTML + CSS, no frameworks.
- Sections: Hero, Features, Rides, Fuel Prices, Iron Butt, Hall of Fame, CTA
  (the Voice section is comment-wrapped out for v1.0)
- Fonts: Barlow Condensed + Barlow via Google Fonts
- Colors: Orange #e05c00 on dark #111/#1a1a1a backgrounds
- Fully responsive
- Fuel prices are Iceland-only (via Gasvaktin); the UK source was dropped in build 37
- App Store call-to-action: "Coming soon to the App Store" on main; the live badge/link
  is on the launch-app-store-badge branch (see Branches)
- Demo Dashboard link: motofuel.app/dashboard?demo=1
- My Dashboard link: motofuel.app/dashboard.html

### support.html — Support (motofuel.app/support)
App Store "Support URL". Shares the site's nav and footer (dark theme, Barlow fonts).
Contact is support@motofuel.app plus an FAQ (data storage, export, fuel-price countries,
languages, iOS version, location use, bug reports).

### privacy.html — Privacy Policy (motofuel.app/privacy)
App Store "Privacy Policy URL". Shares the site's nav and footer. Describes the three uses of
location, each on-device: recording a route while a ride runs, tagging the station when a
fill-up is saved, and showing nearby stations in Stations Near Me. Also states that location
is never sent anywhere, and that **My Dashboard** data imported in the browser is kept only in
that browser's local storage and never uploaded.

### dashboard.html — My MotoFuel Dashboard
Pure HTML + CSS + vanilla JavaScript + Chart.js + Leaflet. No frameworks, no server, no login.

Data flow priority: URL hash > saved data (IndexedDB) > import screen. `normaliseData()` ingests the
app's export (single-bike `{bike, fillups, rides, …}` or multi-bike `{bikes:[…]}`) and computes
per-fill-up stats (see Calculations).

Manufacturer themes:
- BMW → Oswald font, #1C69D4 blue, M stripe
- Honda → Racing Sans One, #CC0000 red
- Kawasaki → Bebas Neue, #39B54A green
- Yamaha → Titillium Web, #0A0A8C dark blue
- Ducati → Playfair Display, #CC0000 red
- KTM → Bebas Neue, #FF6600 orange
- Triumph → Playfair Display, #C5A028 gold
- Harley-Davidson → Rye, #FF6600 orange
- Suzuki → Rajdhani, #004B93 blue
- Default → Barlow Condensed, #e05c00 orange

Sidebar lists bikes (left-aligned) with tabs: Fuel, Rides, Consumables, Tires.

**Fuel tab**
- Stat cards: Avg economy, Total tracked, Fill-ups, Total spent, Total litres, Avg cost/km, Rides
- Range switch **Last 30 · 1 year · All** (`mf-fuel-range`) that drives the three charts **and**
  the fill-up table. "1 year" is measured from the bike's newest fill-up date, not today.
- Charts: Economy (L/100km), Cost per fill-up, Distance per fill-up (Chart.js). Above ~60 points
  the economy line hides its dots.
- Riding-style average bars
- Fill-up history table — full history (not just 50), newest first, with a **Show more** button
  revealing 50 rows at a time. Heading shows `(N)` or `(N of TOTAL)`. The **Station column is
  hidden** until at least one fill-up has a `station_name`.

**Rides tab**
- **Newest / Oldest** sort toggle (`mf-ride-order`); reorders existing cards in place
- Each ride card: distance, duration, avg & max speed, **fill-up count**, and a 🏆 **Iron Butt**
  badge when `is_iron_butt`
- **Show Route** opens a Leaflet map (see Maps) with a **speed chart** (km/h) below it, a
  **"Fill-ups during this ride"** list, and a legend

**Consumables / Tires / Hall of Fame** — chain/sprocket/brake/cush history, tire tracker
(Mounted vs In Garage), retired tires sorted by km.

#### Maps (Leaflet)
- **Tiles:** OpenStreetMap (`tile.openstreetmap.org`) with a CSS dark filter on
  `.leaflet-tile-pane` (invert + hue-rotate). CARTO's dark tiles now require an API key, so OSM
  is used instead; the route line and markers sit in other panes and keep their colours.
- **Full screen:** the real Fullscreen API where supported (Mac/PC/iPad); on iPhone Safari a
  fallback reparents the map container to `<body>` with fixed positioning and locks body scroll.
- **Pins:** green circle at the start (first GPS point), orange (#e05c00) at the end (last point).
- **Fuel markers:** a ⛽ marker per matching fill-up — placed at the exact `station_lat/lng` when
  the app exported them, otherwise estimated along the route from the odometer. The legend's
  "positions are estimated from the odometer" note only shows when at least one marker is estimated.

#### Calculations (match the app)
- **Per-fill-up economy and "+N km"** follow build 38's `getFillups`: fill-ups are sorted by
  odometer; partial fill-ups roll their litres/cost into the next full fill-up; baselines reset
  the accumulator and the anchor; economy is only set on full fill-ups, measured from the last
  full (or `initial_odometer`). "+N km" is the gap to the immediately previous fill-up of any type.
- **Total tracked and cost per km** measure from `bike.initial_odometer` (like the app's
  `getBikeStats`): `total_km = max odometer − initial_odometer` when set, else max − min; cost/km
  = sum of all `total_cost` ÷ total_km.
- **No tire-size correction** — the export doesn't contain the inputs, so the factor is 1.

Privacy: Zero server, data never leaves the browser — the dataset lives in IndexedDB, settings in localStorage.

### dashboard-guide.html — How To Guide
Three methods: One tap from app ("Open in Dashboard"), File import (📤 Export → JSON), and
Export all bikes (⚙️ Settings → Export data → JSON).

## Demo data
`demoData/zzr_demo.json` backs the `?demo=1` dashboard. It was tuned so fill-ups look realistic
(no implausible litres) and so the Reykjavík→Akureyri ride has a matching fill-up at Staðarskáli,
giving the ride map a ⛽ marker and the fill-up list an entry.

## Storage
The imported dataset lives in **IndexedDB** (DB `motofuel-dashboard`, store `kv`) — the all-bikes
export can be 40–50 MB, far over Safari's ~5 MB localStorage quota. Two records:
- `dashboard-data` — the normalised dataset, written after every (non-demo) import.
- `meta` — a tiny `{ hasData, savedAt }` record written alongside the data. On load it's read
  **first** (cheap) to decide restore vs import, before the large dataset is read. If `meta` is
  missing but the data record exists (older saves), it's written and the save is treated as present.
  Both records are cleared by Forget my data and Import new file.
- One-time migration: any legacy `mf_dashboard_data` value in localStorage is moved into IndexedDB
  on load, then that localStorage key is removed.

First paint: an inline `<head>` script adds an **`mf-boot`** class to `<html>` (outside the demo);
its CSS hides the import cards until the load logic picks the loading or import state, so the import
screen never flashes while IndexedDB is read. A **10-second safety timeout** removes `mf-boot` so the
page is never left blank if that logic fails. If saving fails (e.g. private browsing), a notice is
shown rather than failing silently. If IndexedDB is unavailable, the import screen is shown.

localStorage keys (settings only):
- `mf-ride-order` — Rides sort preference (`newest` / `oldest`)
- `mf-fuel-range` — Fuel range switch (`30` / `year` / `all`)

## Tech Stack
- HTML5, CSS3, Vanilla JS — no frameworks
- Chart.js 4.4.1 — charts
- Leaflet 1.9.4 + OpenStreetMap tiles — ride maps
- Google Fonts — Barlow + brand fonts
- Cloudflare Pages — hosting + auto-deploy

## Testing
- Serve locally with `python3 -m http.server 8000`.
- Safari caches aggressively: append `?v=N` (bump N) to force a fresh load, or hard-reload with
  **Cmd+Option+R**.

## Roadmap
- Account Sync — Sign in with Apple + Supabase
- My MotoFuel — full server-synced dashboard
- The Fleet — anonymous community data
