---
name: sketchbook-newsletter
description: Turn a milestone, launch or metrics update into a hand-drawn "sketchbook" HTML newsletter, with pencil-wobbly boxes, a highlighter-marked hero number, a strike-through "before" number, ink arrows, ticks, a taped-in pencil chart and a doodled "thank-you village". Exports email-safe PNG slices for Outlook or Gmail. Use when someone wants a celebration email, team newsletter, milestone announcement or exec update that feels hand-designed rather than templated, or asks for a "pencil sketch", "hand-drawn", "notebook" or "doodle" style.
license: MIT
---

# Sketchbook Newsletter

A one-page newsletter that looks drawn by hand on paper. Every number is still exact. Templated
dashboards get skimmed; a page that looks drawn for this one moment gets read.

## When to use

- A milestone or celebration: "we crossed 1M users", a launch, a year in review.
- A team-wide or leadership update where warmth matters as much as the numbers.
- Any time the user says "hand-drawn", "pencil", "sketch", "notebook", "doodle" or "off-beat".

**Don't use it** for dense operational reporting, or for anything that needs live, searchable
text in the email body. The look depends on web fonts and SVG, which email clients strip, so the
reliable way to send it is as images (see *Sending*).

## The kit

Everything lives in `assets/template.html`: one CSS block and four reusable SVG symbols. Fill the
`{{PLACEHOLDERS}}` and delete any sections you don't need.

| Device | How it is made | Use it for |
|---|---|---|
| Handwriting | Google Fonts **Caveat** (display) + **Kalam** (body), falling back to Bradley Hand and Segoe Print | All text |
| Strike-through "before" | Two hand-drawn SVG strokes over the old number | Hero: what it used to be |
| Highlighter | A skewed `linear-gradient` behind the bottom 42% of the text, with ragged ends | Hero: the new number. One per page |
| Pencil boxes | `::before`/`::after` borders with irregular `border-radius`, run through an `feTurbulence` + `feDisplacementMap` SVG filter; the second outline is offset and faint, like a redrawn stroke | Headline metric, small cards |
| Wavy underline | A cubic-Bézier SVG path under each `h2` | Section headings |
| Ink arrow, tick, house | SVG `<symbol>`s reused with `<use href="#...">` | Lists, wins, the thank-you village |
| Taped chart | White card rotated about 0.8°, two translucent "tape" strips, soft shadow | The one chart |
| Pencil chart | `matplotlib` in `xkcd` mode, a handwriting font, hatched fill, arrow annotations, transparent PNG | The one chart |
| Tilt | `rotate(-2deg…+2deg)` on kicker, notes and sign-off only | Life, used sparingly |

Palette: graphite `#3A3A38`, pencil blue `#2F5BB7`, highlighter yellow `rgb(255,222,74)`, paper
`#FFFFFF`. A graph-paper variant is one `background-image` of two 1px linear gradients at 26px.
White reads better for broad audiences.

## Workflow

1. **Get the story first.** Ask for: the headline number (before → after), 2–3 supporting
   metrics, 2–4 quality metrics, what's next, and who to thank. Never invent numbers.
2. **Draw the chart** if there is a time series:
   ```bash
   pip install -r requirements.txt
   python scripts/sketch_chart.py data.csv --date date --primary users \
     --secondary share --primary-label "weekly users" --secondary-label "share of base" \
     --out chart.png
   ```
   It reads CSV or XLSX, labels the start and end values in hand lettering, trims the
   transparent margin, and treats the secondary series as a 0–1 ratio shown as %.
3. **Fill the template.** Copy `assets/template.html` next to `chart.png` and replace the
   placeholders. Section order (keep it unless told otherwise):
   kicker → hero (strike + highlight) → lede → drivers → **headline box** (big number, sub-metrics,
   taped chart) → **ticked quality cards** → **arrow list** of what's next → **village** →
   sign-off → signature.
4. **Look at it.** Render it and inspect the image, not just the code. Check:
   - labels don't collide in the chart or the village (house labels wrap at 62px);
   - nothing overflows at 390px wide;
   - only one highlighter on the page.
5. **Export for email** (below).

## Writing rules

- Lead with the number and what it means. One sentence of why, then the evidence.
- Sub-metric format: **bold label with the delta**, then one plain sentence.
- Never point out that you beat another team or product. Report your share, not their weakness.
- Keep each list item to a few words, with no sub-text.
- End with a forward-looking line ("On to 10 million.") and a real signature.

## Sending

Email clients drop web fonts, SVG filters and most CSS, so send the newsletter as images:

```bash
python scripts/export_email.py newsletter.html --slices 4
```

This renders at 2x in headless Chrome or Edge, crops to the paper, and cuts the page into slices
**only at blank gaps**, never through text. It writes `email/part-N.png`, `email/full.png` and
`email/paste-into-email.html`. Open that page in a browser, select all, copy, and paste into a new
email. Send yourself a test and check it on a phone before sending wider.

Trade-off to tell the user: recipients can't select or search image text. For a broad exec list
that needs forwardable text, offer a clean typographic version with the same content instead.

## Example

`examples/fieldnote-100k/` is a complete public example for a fictional product, with synthetic
data: `data.csv` → `chart.png` → `newsletter.html` → `email/`.
