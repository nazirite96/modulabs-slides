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
- Author every slide at **1920×1080**; the whole `.deck-stage` scales uniformly to the viewport (JS handles it). Never reflow per device — letterbox instead.
- The `scale()` controller fits via `Math.min(vw/1920, vh/1080)` so the deck is **bullet-proof at any aspect ratio** (ultrawide / portrait / square → clean gray letterbox, never crop or overflow). It reads `window.visualViewport` (falls back to `innerWidth/Height`), rounds the centering translate, and re-fits on `resize` + `orientationchange` + `load` + `visualViewport` resize (handles mobile URL-bar changes). **Keep this controller — don't simplify it back to a plain `innerWidth/innerHeight` resize handler.**
- Switch slides with `.active`/`.visible` (visibility/opacity), never `display`.
- **Keep `.deck-chrome` (top red rule + logo + page counter) and `.progress` INSIDE `.deck-stage`** — they're authored in 1920×1080 coordinates and must scale + letterbox with the slides. If they sit outside the stage (at raw viewport coords) they only line up at exactly 1920×1080 and drift off-center at every other aspect ratio. Screen-fixed tool UI (edit button, notes panel, hint) stays outside the stage.
- Keep content inside the stage; if a slide overflows, **split into more slides** rather than shrinking text below comfortable size.
- Content slides: keep `.pad` top padding ≥230px so content clears the header chrome.

## Animation
- One staggered page-load reveal per slide via `.reveal` + `.d1…d9` delay classes. Use `.new` (yellow flash) to mark elements added on a build-up slide. Keep motion subtle.

## Do / Don't
- DO commit to coral; dominant brand color with crisp white space.
- DO recolor any diagram to the brand palette (outputs = soft-coral, embeddings = light pink, anchors = navy, tokens = white).
- DON'T add gradients-on-white clichés, extra accent hues, or system fonts.
- DON'T put diagram/section content on the coral cover slides — those stay minimal.
