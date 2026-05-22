# Pausality / Somnistics Research Labs — Investor War Room

Hosted at **invest.somnistics.com**. Public landing page + gated inner room.

## Structure

```
investor-war-room/
├── index.html              # Public landing page
├── room/
│   ├── index.html          # Gated inner room (Cloudflare Access protected)
│   ├── safe.html           # SAFE term sheet detail
│   └── financials.html     # Financial model + cap table
├── assets/
│   ├── pausality-deck.pdf      # Drop the current deck here
│   ├── ff-one-pager.pdf        # Drop one-pager PDF here
│   └── ff-safe-term-sheet.pdf  # Drop SAFE term sheet PDF here
├── _redirects              # Cloudflare Pages routing
├── _headers                # Security headers
└── README.md
```

The whole thing is plain HTML + Tailwind via CDN. No build step. Edit a file → commit → push → live in ~30 seconds.

---

## First-time deploy (do this once)

### 1. Create the GitHub repo

```bash
cd "/Users/somnisticshq/Documents/Claude/Projects/Investors/investor-war-room"
git init
git add .
git commit -m "Initial scaffold of investor war room"
```

Then on GitHub: **New repository** → name `investor-war-room` → **Private** → "Push existing repo from command line":

```bash
git remote add origin git@github.com:somnistics/investor-war-room.git
git branch -M main
git push -u origin main
```

(Adjust the org name `somnistics` if your GitHub org is different.)

### 2. Connect Cloudflare Pages

1. Cloudflare dashboard → **Workers & Pages** → **Create application** → **Pages** → **Connect to Git**.
2. Pick the `investor-war-room` repo.
3. Build settings:
   - Framework preset: **None**
   - Build command: *(leave blank)*
   - Build output directory: `/`
4. Click **Save and Deploy**. First deploy completes in ~1 min. You'll get a `.pages.dev` URL — that's your fallback URL.

### 3. Point invest.somnistics.com at it

1. Cloudflare dashboard → **somnistics.com** zone → **DNS** → **Add record**:
   - Type: **CNAME**
   - Name: `invest`
   - Target: `<your-project-name>.pages.dev`
   - Proxy status: **Proxied** (orange cloud)
2. In Pages → your project → **Custom domains** → **Set up a custom domain** → enter `invest.somnistics.com`.
3. Cloudflare provisions an SSL cert automatically. Usually ready in 1–5 min.

### 4. Lock down the /room/* path with Cloudflare Access

This is what gates the inner room without per-investor URLs.

1. Cloudflare dashboard → **Zero Trust** → **Access** → **Applications** → **Add an application** → **Self-hosted**.
2. App config:
   - Name: `Pausality Investor Room`
   - Session duration: `24 hours`
   - Application domain: `invest.somnistics.com/room*`
3. Identity providers: enable **One-time PIN** (sends a 6-digit code to the investor's email — no signup needed).
4. Create a policy:
   - Policy name: `Allowed investors`
   - Action: **Allow**
   - Include: **Emails** → list the investor email addresses one per line, OR **Email ending in** → `@somnistics.com` for internal team.
5. Save. Now anyone hitting `invest.somnistics.com/room/*` gets a Cloudflare email-code login screen.

Add or remove investor emails any time by editing the policy.

---

## Day-to-day updates

```bash
# Edit any file in the project, then:
git add .
git commit -m "Update progress meter — $X committed"
git push
# Live in ~30 seconds via Cloudflare auto-deploy.
```

The progress meter on `index.html` is hand-edited (two width percentages + the dollar labels). When a new commit lands, bump those values and push.

---

## Adding a new investor to the gated room

Cloudflare Zero Trust → **Access** → **Applications** → `Pausality Investor Room` → edit policy → add their email → save. They can immediately request a code at invest.somnistics.com/room.

## Revoking access

Same place — remove email from policy. Their existing session expires at most 24 hours later (per session duration).

---

## What to do if you want to ditch Cloudflare Access and use a shared password instead

Not recommended — passwords leak. But if you want speed-over-security for a weekend, replace step 4 with a single line in `_headers`:

```
/room/*
  WWW-Authenticate: Basic realm="Investor Room"
```

…then add a Cloudflare Page Rule or Worker for HTTP basic auth. Reach back out and I'll wire it up — it's about 20 lines of Worker code.

---

## What lives where

| Thing | Location |
|---|---|
| Public landing copy | `index.html` |
| Progress meter values | `index.html` (search for `data-committed` and `data-target`) |
| Gated SAFE doc | `assets/ff-safe-term-sheet.pdf` (gated by Access on `/room/*` path) |
| One-pager PDF | `assets/ff-one-pager.pdf` |
| Deck PDF | `assets/pausality-deck.pdf` |
| Calendly link | `index.html` + `room/index.html` — currently points at `somnistics-research-labs-1-1-60-min` (solo, 60 min, Zoom). Consider creating a dedicated "Investor pitch" event type in Calendly so you can track investor calls separately from other 1:1s. |
| Allowed-investor emails | Cloudflare Zero Trust → Access policy (NOT in the repo) |

## Things I deliberately left as placeholders / TBDs

These need your input before the page is "done-done":

1. **Pausality logo** — currently a `P` initial in a circle. Drop your real logo file into `assets/` and update the markup in `index.html` / `room/index.html`. Or have Jason hand you the brand mark.
2. **Deck PDF** — page references `/assets/pausality-deck.pdf` but the file isn't in the repo yet. Export the current updated deck (post Brad-name-removal) and drop it in.
3. **SAFE term sheet PDF** — same. Export `Q1 2025 SRL_FF_SAFE_Term_Sheet_2025 3.docx` to PDF and drop into `assets/ff-safe-term-sheet.pdf`.
4. **One-pager PDF** — export the `pausality_friends_family_one_pager.md` from Drive as PDF and drop into `assets/ff-one-pager.pdf`.
5. **MUSC pilot PDF** — copy from Drive (`1dLf9zPDWEZR7ZF9xDQkxPEiGNquwqKyY`) into `assets/pausality-musc-pilot-onepager.pdf`.
6. **SAFE term sheet plain-English summary (`room/safe.html`)** — currently only lists generic terms (round size, instrument, QSBS treatment). The discount/cap, pro-rata, MFN, and other specifics say "See executable term sheet." If you want those visible in the room, edit `room/safe.html` and add the actual values from your executable doc.
7. **Cap-table snapshot (`room/financials.html`)** — founders row says "Walked through on call" because I don't know the actual splits. If you want concrete % visible in the gated room, edit that row.

The repo is private but treat it as if it weren't — don't commit anything truly confidential into it. Real confidential docs live in `assets/` which is gated by the Access policy on the path, not by the repo's private flag.
