# MotoFuel Website — Log

Companion to WEB-ARCHITECTURE.md. Captures claim-sync corrections (site copy vs.
app reality), operational changes (DNS, email, hosting), and notable content
decisions — the "why" that git commits and the page content can't hold on their own.
Newest first.

> Guiding rule: the website must not advertise features the shipping app binary
> doesn't have. When the site leads the app, the claim is pulled back or hidden
> until the app catches up — and logged here so it's restored, not forgotten.

## 2026-07 — Maintenance/informational disclaimer added to site footer
Added a short disclaimer line to the site-wide footer (index, support, privacy):
"MotoFuel is an informational record-keeping tool and is not a substitute for your
manufacturer's maintenance schedule or professional inspection." Brings the website
in line with the in-app Service Intervals disclaimer, ahead of App Store launch. No
dedicated Terms page yet, so the footer line stands alone (fuller version can move
there later).

## 2026-07 — Iron Butt copy corrected to match v1.0 app
Site marketed a dedicated "Iron Butt Mode" with a guided ride-start, automatic
geotagged receipts at every fuel stop, and a "certified" IBA artifact — none of
which are in the v1.0 binary. Corrected to what the app actually does: flag any
completed ride as an Iron Butt attempt, generate a PDF report (route, stats,
fuel stops, receipt photos) to share. Feature card "Iron Butt Mode" → "Iron Butt
Reports" (NEW badge removed); full section steps rewritten; "IBA Ready" pill →
"Share-Ready PDF"; hero + features-intro mentions softened.
RESTORE IN v1.1+: when the dedicated Iron Butt mode ships (Siri start, mandatory
photos, auto-PDF), the fuller "mode" language can return.

## 2026-07 — Voice section removed for v1.0 (manual-first launch)
The landing-page Voice section was removed (comment-wrapped, not deleted) to match
the app's Plan A launch: voice/hands-free/Siri is built but hidden behind
VOICE_ENABLED in v1.0. Dashboard pages confirmed clean of voice copy.
RESTORE IN v1.1: un-wrap the Voice section when voice returns in the app.

## 2026-07 — Support & Privacy pages added (App Store blockers)
support.html (motofuel.app/support) and privacy.html (motofuel.app/privacy) added
for App Store submission — Apple requires live URLs for both (a mailto: isn't
accepted). Dark theme matching the site. Privacy policy makes NO third-party-AI
disclosure because the voice pipeline (OpenAI Whisper / Anthropic Claude) is
disabled and never runs in v1.0 — that disclosure gets added in v1.1 when voice
returns. Both linked in the site footer.

## 2026-07 — Email routing (support@motofuel.app)
Set up support@motofuel.app via Cloudflare Email Routing → forwards to Gmail.
During setup, 6 stale Namecheap email-forwarding DNS records (5 MX + 1 SPF TXT)
were found and removed — they were bouncing mail. Cloudflare's records are now
live; test email received successfully.
