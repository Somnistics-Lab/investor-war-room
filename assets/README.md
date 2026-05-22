# Assets directory

Drop these PDF files in here before deploying:

- `pausality-deck.pdf` — the current updated pitch deck (with Brad Kotansky's name removed from the financials slide).
- `ff-one-pager.pdf` — exported from the F&F one-pager Google Doc.
- `ff-safe-term-sheet.pdf` — exported from `Q1 2025 SRL_FF_SAFE_Term_Sheet_2025 3.docx`.
- `pausality-musc-pilot-onepager.pdf` — the MUSC pilot one-pager (May 19, 2026 version).

These files are gated by Cloudflare Access on the `/room/*` path — they're not publicly accessible.

Once added: `git add assets/ && git commit -m "Add investor PDFs" && git push`.
