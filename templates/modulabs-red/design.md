# 모두의연구소 (Modulabs) Slide Design System

Brand-faithful spec extracted from the official Modulabs red PPT template. Use these exact tokens.

## Palette (CSS variables — already wired in `template.html`)
| Token | Hex | Use |
|---|---|---|
| `--coral` | `#F66B6E` | Cover / Contents / section full-bleed background; primary brand |
| `--red` | `#F7585C` | Accent: top rule, highlights, arrows, section numbers, logo tint |
| `--red-soft` | `#F98386` | Solid soft-coral fills (diagram outputs, segment cells) |
| `--pink` / `--pink-bd` | `#FDE3E4` / `#F3AEB0` | Light pink cells (embeddings), mask chips |
| `--navy` | `#31538F` | Secondary accent: C / `[CLS]` / strong boxes |
| `--bg` | `#F5F7FA` | Content slide base (with white diagonal gradient) |
| `--ink` / `--ink2` / `--muted` | `#333333` / `#5b5b62` / `#9a9aa0` | Body / secondary / captions |

**Monochrome-coral discipline:** the brand is mostly coral + grays. Use navy sparingly as the one secondary accent. Avoid introducing greens/purples/extra hues.

## Typography
- **Display (Latin):** Archivo (700–900) — big headings, "BERT"-style lockups.
- **Body + Korean:** Noto Sans KR (substitutes the template's Malgun Gothic / Pretendard feel).
- **Labels / mono:** JetBrains Mono — eyebrows, page numbers, diagram cell labels.
- All from Google Fonts (already linked). Never use system fonts.

## Signature layout elements (must include)
1. **Cover / Contents / Closing** = coral full-bleed. White logo top-right, heavy white title top-left, white outlined **pill badges**, and the **decorative cluster** bottom-right (two rounded translucent squares + a rotated rounded diamond holding a white **paper-plane** SVG + a hand-drawn **squiggle**).
2. **Content slides** = light bg + faint **diagonal watermark** on the left. Header chrome: a **short red rule + long gray rule** across the top, a **red section number + Korean subtitle** at left (`.shead`), and the **red logo** top-right. Bottom: page indicator + thin red **progress** bar.
3. **Logo:** `assets/modulabs-logo-white.png` (white wordmark). On light slides it is tinted red via CSS `mask-image` + `background:var(--red)` (see `.chrome-logo`). Always keep the asset next to the output HTML.

## Component classes (in `template.html`)
`.shead/.eyebrow/.stitle` header · `.acro` (B**E**RT letter lockup) · `.box`/`.fill` · `.dash`/`.agenda` lists · `.bert`/`.io-bert` model boxes · `.chip(.mask/.cls)` tokens · `.io-row`/`.io-cell` + `.c-out/.c-c/.c-e/.c-tok` (Devlin I/O) · `.stack`/`.blk(.attn/.ff)` (encoder/decoder/Base-Large stacks) · `.emb`(`.tk/.sg/.ps`) embedding-sum table · `.note` callout · `.bubble` speech bubble · `.stat` big number · `.cv/.cv-eng/.cv-pills/.deco` cover · `.ctitle/.clist` contents.

## Fixed-stage rules (non-negotiable)
- **This deck is 16:9 only — NOT responsive.** Author every slide at **1920×1080**. It never reflows: on a smaller/larger screen the whole stage is zoomed uniformly (proportions preserved), never re-laid-out.
- **How the 16:9 fit works (keep it simple):** CSS sizes `.deck-frame` as the largest 16:9 box that fits the window (`width:min(100vw, calc(100vh*16/9)); aspect-ratio:16/9`), the flex `.deck-viewport` centers it, and `scale()` is a **single line** — `stage.transform = scale(frame.clientWidth/1920)`. A window that isn't 16:9 just gets gray letterbox bands; that's a harmless side effect, not a layout to design for. **Don't re-add multi-aspect-ratio math** (`Math.min(vw/1920, vh/1080)`, manual centering translate, ResizeObserver) — it was removed on purpose; the CSS frame handles fitting.
- Switch slides with `.active`/`.visible` (visibility/opacity), never `display`.
- **Keep `.deck-chrome` (top red rule + logo + page counter) and `.progress` INSIDE `.deck-stage`** — they're authored in 1920×1080 coordinates and scale with the slides. Screen-fixed tool UI (edit button, notes panel, hint) stays outside the stage.
- Content slides: keep `.pad` top padding ≥230px so content clears the header chrome. `.pad` is a centered flex column — light slides sit in the optical center instead of jamming at the top.
- **Keep `@page{size:1920px 1080px;margin:0}` + the `@media print` block** (with `print-color-adjust:exact` and `.reveal,.pop{opacity:1}`) — they make browser/headless PDF export come out one slide per exactly-16:9 page with backgrounds and visible body. Without `@page` the browser prints Letter/A4 portrait and the ratio breaks.

## Sizing — fill the fixed canvas (don't leave it sparse)
Because the canvas is a fixed 1920×1080 (not a phone), size components **generously** — timid sizing reads as tiny across a room and leaves the slide 90% empty. Default component sizes (already in `template.html`): `.stitle` 56px · `.eyebrow` 20px · `.ssub` 27px · `.lead` 31px · `.dash li` 30px · `.agenda li` 40px · `.note` 27px · `.box` 22px · `.stat .v` 96px · `.clist .ci` 33px. Body text floor **~28px**; never go below ~22px.
- **Use the whole width and height.** Prefer wide content blocks and 2-column (`.grid2`) layouts over a narrow left column with a big void on the right. Aim for content to occupy the majority of the below-header band, not a small cluster.
- If a slide feels empty, **add substance** (a supporting note, a diagram, a second column) — or merge two thin slides. If it overflows, **split into more slides** rather than shrinking text below the floor.
- The tightly-tuned BERT-diagram primitives (`.io-cell`/`.blk`/`.emb`/`.chip`) are laid out relative to each other — scale a whole diagram with a wrapper transform if needed, rather than editing those base sizes piecemeal.

## Animation
- One staggered page-load reveal per slide via `.reveal` + `.d1…d9` delay classes. Use `.new` (yellow flash) to mark elements added on a build-up slide. Keep motion subtle.

## Do / Don't
- DO commit to coral; dominant brand color with crisp white space.
- DO recolor any diagram to the brand palette (outputs = soft-coral, embeddings = light pink, anchors = navy, tokens = white).
- DON'T add gradients-on-white clichés, extra accent hues, or system fonts.
- DON'T put diagram/section content on the coral cover slides — those stay minimal.
