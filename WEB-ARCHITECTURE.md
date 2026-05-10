# motofuel.app — Web Architecture

## Hosting
- **Platform:** Netlify
- **Repo:** github.com/thereykjavikrider/motofuel-website
- **Deploy:** Auto-deploys on every `git push` to main
- **Domain:** motofuel.app (primary) + www.motofuel.app (redirects)
- **SSL:** Let's Encrypt via Netlify, auto-renews

## File Structure
motofuel-website/
  ├── index.html           — Landing page (motofuel.app)
  ├── dashboard.html       — My MotoFuel Dashboard
  ├── dashboard-guide.html — How to use the dashboard
  ├── WEB-ARCHITECTURE.md  — This file
  └── .gitignore           — Excludes .DS_Store

## Pages

### index.html — Landing Page
Pure HTML + CSS, no frameworks.
- Sections: Hero, Features, Voice, Rides, Fuel Prices, Iron Butt, Hall of Fame, CTA
- Fonts: Barlow Condensed + Barlow via Google Fonts
- Colors: Orange #e05c00 on dark #111/#1a1a1a backgrounds
- Fully responsive
- TestFlight link: testflight.apple.com/join/14WTnnSh
- Demo Dashboard link: motofuel.app/dashboard#BASE64_DATA
- My Dashboard link: motofuel.app/dashboard.html

### dashboard.html — My MotoFuel Dashboard
Pure HTML + CSS + vanilla JavaScript + Chart.js. No frameworks, no server, no login.

Data flow priority: URL hash > localStorage > import screen

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

Dashboard sections per bike:
1. Bike header — name, make, model, year + stat cards
2. Economy chart — L/100km over time
3. Cost chart — cost per fillup
4. Distance chart — km per fillup
5. Riding style — on road/off road/in town bars
6. Fillup table — last 50 fillups
7. Ride history — cards with distance, duration, speed
8. Tire tracker — Mounted vs In Garage badge
9. Hall of Fame — retired tires sorted by km

Privacy: Zero server, data never leaves browser, localStorage only.

### dashboard-guide.html — How To Guide
Three methods: One tap from app, File import, Export all bikes.

## Tech Stack
- HTML5, CSS3, Vanilla JS — no frameworks
- Chart.js 4.4.1 — charts
- Google Fonts — Barlow + brand fonts
- Netlify — hosting + auto-deploy

## Roadmap
- Browser Save button
- Account Sync — Sign in with Apple + Supabase
- My MotoFuel — full server-synced dashboard
- The Fleet — anonymous community data
- Move Export All Bikes to main screen
