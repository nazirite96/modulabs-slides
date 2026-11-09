---
name: modulabs-slides
description: Build a distinctive self-contained 16:9 HTML slide deck from a reusable template library — ships with the 모두의연구소 (Modulabs) red brand template (coral cover/section slides, red+gray header rule, brand-recolored diagrams, presenter notes, inline editing) and supports adding new templates extracted from a reference PPTX/PDF/image/URL. Use when the user wants a Modulabs-styled or other branded HTML presentation, an HTML PPT in "this template" style, to convert notes/a topic/a video transcript into a branded deck, or to add a new deck template. Trigger on "모두의연구소 스타일", "modulabs slides", "이 빨강 템플릿으로 슬라이드", "브랜드 덱 만들어", "새 템플릿 추가".
user-invocable: true
allowed-tools: Read, Write, Edit, Bash, AskUserQuestion, WebFetch
---

# 모두의연구소 Slides — branded HTML deck generator (template library)

Generates a single self-contained HTML deck (zero dependencies, fixed 16:9 stage scaled to viewport). Templates live in a **library** under `${CLAUDE_SKILL_DIR}/templates/`:

- `templates/index.md` — the registry (one row per template: id, name, look, logo).
- `templates/<id>/template.html` — that template's starter deck (theme + component library + cover/contents/content/closing examples + nav/edit/notes/presenter-window JS).
- `templates/<id>/design.md` — that template's design system (palette, fonts, signature elements, component classes, rules).
- `templates/<id>/assets/` — that template's brand assets (e.g. a logo) that the deck references and that must be copied next to the output HTML.

The bundled default is **`modulabs-red`** (the official Modulabs red brand).

Two workflows: **A. Generate a deck** (Steps 1–6) and **B. Add a new template** (separate section below). Pick by the user's intent.

---

# A. Generate a deck

## Step 1 — Pick the template
Read `${CLAUDE_SKILL_DIR}/templates/index.md`.
- If the user named a template (or said "modulabs / 빨강 / 브랜드"), use it.
- If only one template exists, use it.
- Otherwise list the templates (name + one-line look) and ask with **AskUserQuestion** which to use. Default = `modulabs-red`.

Let `<tpl>` = `${CLAUDE_SKILL_DIR}/templates/<id>`.

## Step 2 — Read the chosen design system
Read `<tpl>/design.md` and skim `<tpl>/template.html` so you reuse the exact palette, fonts, chrome, and component classes. **Do not invent a new palette** — adopt the template's.

## Step 3 — Get the content (ask only what's missing)
Determine: deck topic/title, the section/chapter breakdown, and per-slide content. Sources can be: text the user pastes, rough notes, a topic to expand, a video transcript (if a video, the user may have used the `watch` skill — reuse those frames/transcript), or a PPTX/PDF to convert (see the PPTX-conversion tips below). If content is thin, ask 1–2 focused questions (purpose, approx length, language). Default language: match the user (Korean-first, keep English technical terms/diagram labels).

## Step 4 — Scaffold the deck
1. Create an output folder for the deck (or use the project root). Copy the template's assets so the deck can find them:
   ```bash
   mkdir -p <out>/assets
   cp "<tpl>/assets/"* <out>/assets/
   ```
2. Copy `<tpl>/template.html` to `<out>/<name>.html` as the starting point.
3. Fill the `{{PLACEHOLDERS}}` and **add one `<section class="slide">` per slide**, reusing the component classes from `<tpl>/template.html` / `<tpl>/design.md`. Typical archetypes (modulabs-red naming; adapt per template):
   - **Cover**: title + subtitle + pill tags + deco cluster.
   - **Contents / section divider**: `.ctitle` + `.clist`, or a big section number.
   - **Content**: `.shead` header (section number + subtitle) + body built from components. Put `data-ct="NN · 섹션명"` on every content slide (drives the chrome + page counter; omit on cover/closing).
   - **Closing**: thanks + CTA pill.
4. **Diagrams:** recolor everything to the template's palette (in modulabs-red: outputs `.c-out` soft-coral, embeddings `.c-e` light pink, anchors/`[CLS]` `.c-c`/`.chip.cls` navy, tokens `.c-tok` white, model box `.bert`/`.io-bert` white + accent border; stacks via `.stack`/`.blk`).
5. **Presenter notes:** update the `NOTES` array (one `{h, b}` per slide, in order). It drives both the `N` notes panel and the `P` / `⌘P` presenter window. **Verify `NOTES.length === slide count`.**
   - `b` is that slide's script. Split paragraphs with `\n\n` (both views keep the line breaks).
   - Shared SVG defs that live outside the slides (a `<symbol>` / `<marker>` / gradient sprite referenced through `<use href="#…">` or `url(#…)`) go in a zero-size element with `data-sprite`, e.g. `<svg data-sprite width="0" height="0" style="position:absolute" aria-hidden="true">`. The presenter window copies those into its previews; without it, symbol-based drawings come out blank there.
6. Keep nav (arrows/space/wheel/swipe), edit mode (`E`), notes (`N`) and the presenter window (`P` / `⌘P` / `Ctrl+P`) intact.

## Step 5 — Density & sizing (fill the fixed canvas)
This is a **fixed 16:9 canvas, not a responsive/phone layout** — size components generously so they fill it. Timid sizing reads as tiny across a room and leaves the slide mostly empty. Keep body text ≥~28px (never below ~22px); use the template defaults (`.stitle` 56 · `.dash li` 30 · `.note` 27 · `.box` 22 — see `design.md` → *Sizing*). Use the full width (wide blocks, `.grid2`) rather than a narrow column with a big void beside it; `.pad` already vertically centers a light slide. If a slide feels empty, **add substance or merge**; if it gets crowded, **split it into more slides** (and add matching `NOTES` entries) rather than shrinking text. Keep `.pad` top padding ≥230px on `.shead` slides so content clears the header.

## Step 6 — Verify with headless screenshots (required)
Render the riskiest slides (cover, a header/diagram slide, the densest slide, closing) and inspect for overflow / overlap / broken glyphs / missing logo. **Render from inside the deck's folder (or with an absolute `file://` path to it) so the relative `assets/` paths resolve** — a temp copy in `/tmp` will show a broken logo and missing images:
```bash
CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"   # or any Chromium
cd <out>
for N in 0 2 5 LAST; do
  sed "s/this.show(0);/this.show($N);/" <name>.html > _v$N.html
  "$CHROME" --headless=new --disable-gpu --hide-scrollbars --window-size=1280,720 \
    --virtual-time-budget=2600 --screenshot="/tmp/_s$N.png" "file://$PWD/_v$N.html"
done
rm -f _v*.html
```
Read each PNG; fix issues; re-verify. Render at a **16:9 window** (e.g. `1920,1080` or `1280,720`) — that's the only supported ratio. On each slide check: no overflow/overlap, no broken glyphs, logo present, and **the content fills the canvas** (body isn't a small cluster stranded in a sea of empty space — if it is, go back to Step 5 and enlarge/add/merge).

(Aspect ratio itself needs no per-deck check: the CSS 16:9 frame guarantees it. Off-16:9 windows just letterbox — not a layout to verify.)

## Step 7 — Deliver
`open <name>.html`. Tell the user: file path, slide count, and shortcuts — **arrows/space/wheel/swipe** to navigate, **N** presenter notes, **P / ⌘P / Ctrl+P** presenter window (current + next slide, script, timer, font size; synced both ways — the first time, the browser may ask to allow pop-ups for the file), **E** inline edit (Ctrl+S save). Note the deck depends on the files in `assets/` — keep `assets/` beside the HTML when moving/exporting (PDF export or Vercel deploy: include the folder).

**PDF export** (the template's `@page{size:1920px 1080px}` rule keeps pages exactly 16:9 — never remove it):
```bash
"$CHROME" --headless=new --disable-gpu --no-pdf-header-footer \
  --print-to-pdf=<name>.pdf "file://$PWD/<name>.html"
```
(or print from the **deck window** through the browser menu — macOS **File → Print**, Chrome/Edge **⋮ / ⋯ → Print** — with margins "None", or Chrome's system print dialog (**Ctrl+Shift+P** on Windows/Linux, **⌥⌘P** on macOS). The ⌘P / Ctrl+P shortcut opens the presenter window, not the print dialog.) Verify the output: pages must be 1920×1080 (16:9), one slide per page, backgrounds included.

### Converting a PPTX/PDF into a deck (preserve & rearrange assets)
- Extract: `unzip -o file.pptx -d /tmp/ref`. Slides are `ppt/slides/slideN.xml`; per-slide images are listed in `ppt/slides/_rels/slideN.xml.rels`; all media is in `ppt/media/`.
- Render a visual reference: `soffice --headless --convert-to pdf --outdir /tmp "file.pptx"`, then Read the PDF pages to see each slide's true layout.
- Pull per-slide text from `<a:t>` runs and the image filenames from the rels, so you know which media belongs on which slide.
- **Copy every content image into `<out>/assets/` and reference it** — preserve all assets, then re-lay them out in the template's component cards.
- **Verify each shared or formula image's actual content before placing it** (Read the PNG or check dimensions with `sips -g pixelWidth -g pixelHeight`). Filenames are not self-describing and the same image can appear on several slides — guessing the role leads to mislabeled/misplaced figures.
- **Black-background formula/screenshot images** look wrong floating on a light slide — wrap them in a dark card (e.g. a `.fig.dark`-style `#15161a` rounded box) so they read as an intentional "derivation board".

---

# B. Add a new template

Use this when the user wants a different look — their own brand, a client's deck, or to match a reference **PPTX / PDF / image / URL**. You create a new self-contained template under `templates/<id>/` by adapting the default.

## B1 — Gather the reference & extract design tokens
- **PPTX:** `unzip -o file.pptx -d /tmp/ref`. Exact theme colors: `grep -o 'srgbClr val="[0-9A-Fa-f]*"' /tmp/ref/ppt/theme/theme1.xml | sort -u`. Fonts: the `<a:latin>` / `<a:ea>` typefaces in the theme. Logos/brand art: `ppt/media/*` (preview with Read). Render to PDF (`soffice --headless --convert-to pdf`) and Read it to capture cover style, header chrome, and layout signatures.
- **PDF:** Read it directly for colors/layout; pull any logo via screenshot/crop.
- **Image(s):** Read them; infer the dominant palette and a primary + accent + bg + ink.
- **URL:** `WebFetch` (and/or a headless screenshot) for palette, fonts, and layout cues.
- Settle on: **palette** (primary, accent, bg, ink + 1–2 secondaries with exact hex), **fonts** (display / body / mono — prefer Google Fonts equivalents), **signature layout** (cover treatment, header chrome, logo placement, any deco), and a **logo asset** if there is one.

## B2 — Scaffold the template folder (copy the default, then adapt)
```bash
TPL="${CLAUDE_SKILL_DIR}/templates/<id>"        # <id> = short kebab-case, e.g. acme-dark
mkdir -p "$TPL/assets"
cp "${CLAUDE_SKILL_DIR}/templates/modulabs-red/template.html" "$TPL/template.html"
cp "${CLAUDE_SKILL_DIR}/templates/modulabs-red/design.md"     "$TPL/design.md"
# drop the extracted logo / brand art into "$TPL/assets/"
```
Then edit `$TPL/template.html`:
- Rewrite the `:root` CSS variables to the new palette and swap the Google Fonts `<link>` + `--sans`/`--lat`/`--mono`.
- Adapt the cover / section / closing slides, the header chrome, the logo (`mask-image` / `background`) and the deco cluster to the reference. **Keep the mandatory fixed-stage base, the `@page`/`@media print` block, the `.slide`/`.active`/`.visible` switching, and the nav/edit/notes/presenter-window JS intact** (don't break the controller, the `P` / `⌘P` presenter window or the 16:9 PDF export).
- Update the asset filename(s) the CSS/HTML references to whatever you put in `$TPL/assets/`.

Rewrite `$TPL/design.md` to document the new palette, fonts, signature elements, and component classes — this is the file the generator reads at deck time, so it must be accurate. Carry over its **Presenter tools** section (N panel, P / ⌘P window, notes line breaks, `data-sprite` rule) unchanged.

## B3 — Register it
Add a row to `${CLAUDE_SKILL_DIR}/templates/index.md` (`id`, name, one-line look, logo asset filename).

## B4 — Verify the template
Render the new `template.html`'s cover + one content slide with the headless command in Step 6 (from inside `$TPL`) and Read the PNGs. Fix palette/font/logo/overflow issues before telling the user it's ready. Report the new template id and that it's now selectable in Step 1.

---

## Notes
- The Modulabs logo is the user's own org brand (modulabs.co.kr) — legitimate to use in their decks. When adding another brand's template, it's for the user's own/authorized use.
- Templates are fully independent: editing or adding one never changes another. `modulabs-red` is the default; never delete it.
