# Grand Tour Field Guide

The house style of the **grand-tour** skill's print edition: a booklet that teaches someone to *explain* a codebase, carried to a coffee shop and filled in by hand. It looks like a mid-century field guide or survey manual, with square, hairline, grayscale pages and a serif you read for an hour. Every booklet, whatever the repo, should be recognisably the same object.

**Scope.** This style is for grand-tour booklets and their companion cards (talking-points card, key sheets). It is not a general brand. A pitch deck in this style would be wrong.

**How to use it.** Copy this folder next to your booklet's HTML (or link to it in place), keeping `components/` and `fonts/` as siblings. Link `components/fonts.css`, then `components/bundle.css`, put `class="fg-doc"` on `<body>`, and use the classes in [Components](#components). Don't restyle it; override only the `@page` running head (repo name, print date). `../../examples/replicate-predictions-downloader.html` is a complete booklet in this markup and the quickest reference. Everything else in this document explains *when* and *why*.

## Voice

- **Humor lives in the prose and the metaphor. Labels, code, diagrams and paths stay literal.** A callout tag says `WHERE THE METAPHOR BREAKS`, never something cute.
- Write like Martin Kleppmann: classic style, concrete, one idea per paragraph, transitions that feel like walking from room to room.
- **Second person, to the builder.** The reader wrote this code. Say "your ledger reads the bookmark", never "the author" and never as if briefing a reviewer.
- **How before why.** Most of the page is mechanism: calls in order, data in and out, real names. A reason appears only when it explains the mechanism.
- **Honesty is visible and complete.** Problems live in the Loose Ends section, each with a `confirmed` or `suspected` badge. A suspicion is never set in the solid badge. No rationale badges elsewhere. Loose Ends is as long as the findings need. Security findings and confirmed correctness bugs each get a paragraph and are never trimmed for space; only minor items shrink to one line.
- **Paper travels.** Never print a secret value. Describe security issues by file and category ("this route needs an auth check"), never as a method.

## Visual foundations

**Grayscale only.** Booklets get photocopied. `ink` does all the work and `ink-muted` carries labels. `rule-soft` and `rule-hair` are for lines, never text. No colour ever carries meaning. Meaning comes from **line style**:

| Line | Means |
|---|---|
| solid outline | confirmed, or a plain label |
| solid ink fill, paper text | confirmed (strongest) and Explain-how exercises |
| dashed outline | inferred, suspected, soft, uncertain, a reply, a place to write or stamp |
| 45° hatch | outside the tool (a service you don't own) |

**Square and ruled.** `radius-none` everywhere. The only round shape is the passport stamp (`radius-stamp`). Depth comes from rule weight (`stroke-hair` → `stroke-frame`), never shadows.

**Type.** Three families, never more:
- **Charter** (`body`) is the reading face: 10.4pt/1.42, hyphenated.
- **TeX Gyre Heros Condensed** (`label`) does everything structural: headings, kickers, badges, running heads, page numbers, diagram titles. Bold for headings, regular for running heads. Labels are uppercase and tracked +0.10 to +0.16em.
- **DejaVu Sans Mono** (`mono`) sets code at 8.3pt, and every real path in a diagram.

Charter here is converted from Bitstream's Type 1 release, so it covers Latin-1 only. Arrows (→), Greek (κ) and ★ fall back to another font. That's acceptable in running text; in labels, prefer Heros, which has them.

**Page.** US Letter. Margins top 0.6in, sides 0.78in, bottom 0.66in. The running head (top left) is `THE GRAND TOUR · <repo> · FIELD GUIDE`, the print date is top right, and the page number bottom centre. The cover has none of these. Each booklet overrides `@top-left` / `@top-right` content in its own `@page` block.

## The booklet, in order

1. **Cover** (`.cover`): top rule with "The Grand Tour / Print Edition", the repo name huge, an italic subtitle naming the metaphor, the boxed promise, and a 3-column meta grid (repo, version toured, printed, source size, tests, sitting).
2. **How to use + What You Built** (one `.sec`): a three-sentence lede, a dotted-leader contents (`.toc`) grouped *The Guide / The Work / Behind the stop sign*, one line on the badges, then the What You Built section.
3. **The Guide**: The Map (with "where things live"), Follow One Run (the centerpiece, 2–3 pages of `.hop`s), The Parts (`.cast` table of in → out), then Proper Names and Loose Ends, sharing a page when Loose Ends is short and a page each when it isn't. **Talking Points never go here.**
4. **Field Exercises**: 8 in all. Four objective ones on the first page, then four Explain-how, **two per page**.
5. **STOP divider**: a full page.
6. **Answer Key, Talking Points, Passport, Bring It Back**, run on without forced breaks. Key entries are ingredients, never a model paragraph.

Target is 14–16 pages, up to 18 when Loose Ends needs the room. When it runs long, trim prose or pair sections. Never cut the writing lines or a protected Loose End (security, confirmed correctness bug).

## Print mechanics (not expressible in CSS)

- **Render with Chromium** (`page.pdf(print_background=True, prefer_css_page_size=True)`), because paged-media margin boxes need it.
- **Page references are two-pass.** Render once, find each section's h2 line ("Title … NN") in `pdftotext -layout` output (skip the contents page), fill in `p. N`, then render again. Invisible marker spans get dropped at page tops, so don't use them.
- **Two Explain-hows per page.** At `line-pitch` 0.25in with 5 draft lines each, an Explain-how is about 4.4in tall. Anything taller breaks the pairing. Measure in print media, not screen.
- **Check a map path at 100 dpi.** If it isn't monospace, a stylesheet is overriding the SVG's classes.
- **Rasterize and look before handing over.** Contact-sheet every page. Look for orphaned lines, a section spilling one paragraph onto a new page, and diagram labels colliding with arrows. The fix is usually to trim or pair sections, never to shrink type below these styles.

## Diagrams (the Map)

One inline SVG, 680 wide. Zones are dashed horizontal lines labelled in tracked `ink-muted` caps: `OUTSIDE (NOT YOURS)`, `INSIDE THE TOOL (<pkg>/)`, `YOUR DISK`, or whatever zones fit the project. Boxes use the `label` face bold for the name and `mono` for the **real path** (every box has one). Outside boxes are hatched and dashed. Requests are solid arrows, replies dashed. Arrow labels are `mono` 8.8px with a 4px white halo (`paint-order: stroke`) and are **drawn last**, so crossings stay legible. Give the SVG's own `<style>` classes their font families (`mono` for paths and labels). `bundle.css` sets the label face on the `<svg>` only as an inherited default, so your classes win. Place each container directly above what it writes to, so arrows run straight down. The first draft of a map always collides somewhere, so check it at 100 dpi.

## Components

Plain HTML plus the classes in `components/bundle.css`; there is no JavaScript. The example booklet uses nearly all of them.

| Component | Markup | Rules |
|---|---|---|
| Cover | `section.cover` › `.top`, `h1`, `.sub`, `.promise > b`, `.meta > div > b` | No running head. Repo name at 64pt, broken only at an existing hyphen. Meta numbers are measured (commits, lines, tests), never estimated. |
| Section | `section.sec` › `h2 > span.no` | Each `.sec` starts a page. The h2 text must be unique and match the contents list, because the page finder searches for it. |
| Contents | `ol.toc` › `li.grp`, `li > span.n + text + span.fill + span.p` | Grouped *The Guide / The Work / Behind the stop sign*. Page numbers come from the second render pass. |
| Callout | `.callout` › `span.tag`; `.callout.soft` | Solid bar for things that are true and matter; dashed for asides. The tag is literal, never a joke. At most two per page. |
| Badge | `span.badge.inv` (confirmed), `span.badge.sus` (suspected) | Only in Loose Ends. Never set a suspicion in the solid badge. |
| Map | `div.map > svg` | See [Diagrams](#diagrams-the-map). |
| Table | `table`, `table.cast` | No vertical rules. `.cast` is The Parts: method in the first column, in → out and an italic `span.blame` "check here when…" line in the second. |
| Journey hop | `.hop` › `.num`, `div` › `.where`, `h3`, prose, optional `pre > code` | Six to ten per run. Toy data that is clearly toy; never a real secret or working URL. |
| Code block | `pre > code` | Eight lines at most, marked "simplified" if trimmed. Never breaks across pages. |
| Objective exercise | `.ex` › `.ex-head` (`.id`, `.kind`, `.dist`), `p`, `.lines > .ln×N` | Find it / Predict it / Trace it. 4–6 lines, sized to the answer. |
| Explain-how exercise | `.ex` with `.kind.explain`, `.brief`, `.draft-label`, `.lines`, `.selfcheck > b + .boxes > span×4` | 5 lines per draft (6 for five-sentence budgets) so two fit on a page. The self-check comes between Draft 1 and Draft 2. |
| Stop divider | `section.divider` › `.big`, `p`, `.fold` | A whole page, once, between the exercises and the answer key. |
| Answer key entry | `.key` › `.kh > span`, `dl > dt + dd` | Objective: Answer, Stamp if. Explain-how: Must hit, Proper names, Traps, Altitude. Never a model paragraph. Open the key with `.stamp-rule`. |
| Talking points | `.alt > span.kicker + p`; `.qa > p.q + p` | Behind the divider only. |
| Passport | `.passport > .stamp` › `.ring`, `.name`, `.which` | One dashed ring per district; the only round shape in the system. |
| Helpers | `.lede`, `.small`, `.muted`, `.keep` (no break inside), `.two` (two columns), `.pref` (page reference) | |
