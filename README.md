# Pausality / Somnistics Research Labs — Investor War Room

A single-page F&F round site, hosted free on GitHub Pages.

**Live URL** (once deployed): `https://YOUR-USERNAME.github.io/investor-war-room/`

## Structure

```
investor-war-room/
├── index.html         # The whole page
├── assets/            # PDFs you drop in (deck, one-pager) — see assets/README.md
├── .gitignore
└── README.md
```

Plain HTML + Tailwind via CDN. No build step. Edit → commit → push → live in ~30 seconds.

The actual SAFE term sheet and detailed financials are **not** in the repo — those you share directly with serious investors via email after a call.

---

## First-time deploy (60 seconds, do this once)

Paste this whole block into Terminal on your Mac. If it errors at `gh --version` you need to `brew install gh` first.

```bash
cd "/Users/somnisticshq/Documents/Claude/Projects/Investors/investor-war-room"

# Verify prerequisites
git --version >/dev/null && gh --version >/dev/null || { echo "MISSING — run: brew install gh"; exit 1; }
gh auth status >/dev/null 2>&1 || gh auth login

# Init, commit, create on GitHub (public — required for free GitHub Pages), push
git init -q && git add . && git commit -qm "Initial scaffold of investor war room"
gh repo create investor-war-room --public --source=. --remote=origin --push

# Enable GitHub Pages on the main branch (root folder)
OWNER=$(gh repo view --json owner --jq '.owner.login')
gh api -X POST "repos/$OWNER/investor-war-room/pages" -F "source[branch]=main" -F "source[path]=/" >/dev/null \
  && echo "✅ Pages enabled — live at: https://$OWNER.github.io/investor-war-room/ (allow 1–2 min for first build)"
```

The last line prints your live URL. First deploy takes 1–2 minutes; subsequent pushes update in ~30 seconds.

---

## Day-to-day updates

```bash
# Edit any file, then:
git add . && git commit -m "Update progress meter — $X committed" && git push
# Live in ~30 seconds.
```

The progress meter on `index.html` is hand-edited. Search for `data-committed` and `data-target` and update the dollar labels. Search for `width: 40%` and `width: 10%` to adjust the bar widths if commitments change.

---

## What to drop into `assets/`

The page itself doesn't load any PDFs yet — but if you want to make the deck downloadable for warm-intro forwarders:

1. Export your current updated deck (post Brad-Kotansky-removal) to PDF as `assets/pausality-deck.pdf`.
2. Export the F&F one-pager from Drive to PDF as `assets/ff-one-pager.pdf`.
3. Commit + push.
4. Add a link to either file from `index.html` (e.g. `<a href="/assets/pausality-deck.pdf" download>Download deck</a>`).

**Do NOT commit:**
- The executable SAFE term sheet (share via DocuSign / email only)
- Real cap table (share on calls only)
- Anything truly confidential — the repo is public

---

## If you want a custom domain later (`invest.somnistics.com`)

GitHub Pages supports custom domains for free:
1. In the repo's `Settings → Pages → Custom domain` field, enter `invest.somnistics.com`.
2. At your DNS host (Cloudflare, Namecheap, wherever), add a CNAME record: `invest` → `YOUR-USERNAME.github.io`.
3. Wait ~5 min, then enable "Enforce HTTPS" in the same settings panel.

No Cloudflare account needed for this — works with any DNS host.

---

## What lives where

| Thing | Location |
|---|---|
| Page copy | `index.html` |
| Progress meter values | `index.html` (search for `data-committed`, `data-target`) |
| Calendly link | `index.html` (search for `calendly.com`) |
| Email Randy mailto | `index.html` (search for `mailto:`) |
| Actual SAFE term sheet, cap table, financials | NOT in this repo — emailed/DocuSigned directly to serious investors |
