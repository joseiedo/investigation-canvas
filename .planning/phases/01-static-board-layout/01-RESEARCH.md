# Phase 1: Static Board Layout - Research

**Researched:** 2026-02-24
**Domain:** Pure HTML/CSS static layout — full-viewport board, cork texture, styled evidence cards
**Confidence:** HIGH

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **Card visual style**: Base material is index card / notecard style, all cream/off-white (uniform color). Each card has a slight random tilt (±2–5°) to feel physically hand-pinned. Pin is a round thumbtack head (red or colored) as a CSS pseudo-element at top center. Strong drop shadow (high offset, soft blur) to give cards lift off the board.
- **Board atmosphere**: Background tone is dark, moody cork (deep brown/charcoal) — high contrast against cream cards. Texture via CSS noise pattern using radial-gradients and grain — no image assets. Ambient elements: edge vignette (darkened corners/edges) + subtle film grain overlay on the board. Overall feel: cinematic thriller.
- **Card positioning**: Cards are naturally scattered at varied positions — no grid, no obvious pattern. Minor overlaps allowed (as long as titles remain readable). Positions defined as percentages (viewport-relative) so layout adapts to window size.
- **Card content**: 5 cards with generic detective case labels (e.g., "Suspect A", "Victim", "Crime Scene", "Alibi", "Witness"). Each card shows title only — no body text, no tags. Font: typewriter / monospace (Courier-style).
- **Tech Stack**: HTML + CSS + JS only — no npm, no React, no Canvas libraries. Single browser file or minimal file set.

### Claude's Discretion
- Exact percentage coordinates for each card's initial position
- Specific CSS technique for noise/grain texture
- Pin pseudo-element sizing and exact color shade (red family)
- Card dimensions (width × height ratio appropriate for an index card)
- Exact shadow values within the "strong/dramatic" direction

### Deferred Ideas (OUT OF SCOPE)
None — discussion stayed within phase scope.
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| BOARD-01 | Board fills the full viewport (100vw × 100vh) with a cork board texture background and no visible scrollbars | Full-viewport CSS reset pattern; CSS noise texture via inline SVG + feTurbulence; vignette overlay via radial-gradient pseudo-element |
| BOARD-02 | 5 pre-set evidence cards (rectangles and/or squares) are displayed on the board with individual title labels, hard-coded in HTML | Absolute positioning with percentage-based top/left; CSS transform: rotate() for tilt; ::before thumbtack pseudo-element; box-shadow layering; Courier New font stack |
</phase_requirements>

---

## Summary

This phase is a pure HTML/CSS static layout problem. No JavaScript is needed — Phase 1 is entirely declarative. The project constraint (no npm, no frameworks, single file) means everything must be achievable with standard CSS features available in all modern browsers.

The two hardest sub-problems are (1) creating the cork board texture without image assets and (2) making the cards feel physically real rather than like UI elements. Both are solvable with well-understood CSS techniques: SVG `feTurbulence` embedded inline as a data URL provides the grain, and layered `box-shadow` with a high offset creates the physical lift. The vignette and grain overlay are stacked as `::after` pseudo-elements on the board container. Vendor support for all techniques used is universal in modern browsers.

The card pin is the only tricky pseudo-element — it must use `::before` on the `.card` element, meaning the title text cannot also use `::before`. The title goes in a `<span>` or direct text node. Card tilt uses inline `style="transform: rotate(Xdeg)"` on each card so five distinct tilts can be set without per-card CSS rules.

**Primary recommendation:** Build a single `index.html` with embedded `<style>`. Board = `<div id="board">`, cards = `<div class="card">` with inline style for position and rotation. Use inline SVG data URL for grain texture on the board. Layer a `::after` radial-gradient vignette on top.

---

## Standard Stack

### Core
| Technology | Version | Purpose | Why Standard |
|------------|---------|---------|--------------|
| HTML5 | — | Structure: board container + 5 card divs | Project constraint; zero dependencies |
| CSS3 | — | All visual styling, layout, texture, shadows | Project constraint; all required features are in CSS3 baseline |
| No JavaScript | — | Phase 1 has no interactivity | JS is needed only in Phase 2 (drag) |

### Supporting Techniques
| Technique | Purpose | When to Use |
|-----------|---------|-------------|
| SVG `feTurbulence` inline data URL | Cork/grain texture without image files | Board background grain layer |
| `radial-gradient` pseudo-element | Vignette (dark edges/corners) overlay | Board `::after` layer |
| CSS `box-shadow` (layered) | Physical card lift effect | Each `.card` element |
| CSS `transform: rotate()` | Card tilt for hand-pinned feel | Inline style per card |
| `::before` pseudo-element | Thumbtack pin at top center of card | Each `.card::before` |
| `position: absolute` + `%` units | Scattered card positioning | Each `.card` element |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Inline SVG data URL for grain | External `.svg` file | External file violates single-file delivery goal |
| Inline SVG data URL for grain | CSS `repeating-radial-gradient` noise hack | feTurbulence produces more organic, realistic grain |
| CSS `transform: rotate()` inline | Per-card CSS classes `.card--tilt-1` etc. | Inline style is simpler for 5 distinct values; no extra CSS rules |
| Layered `box-shadow` | Drop-filter shadow | `box-shadow` has wider support, more predictable rendering |

**Installation:** None — no packages, no build tools, no npm.

---

## Architecture Patterns

### Recommended File Structure
```
investigation-canvas/
├── index.html          # Single file: HTML structure + embedded <style>
```

All CSS lives in a `<style>` block inside `index.html`. No external stylesheets needed for Phase 1.

### Pattern 1: Full-Viewport Board with No Scrollbars

**What:** Reset body margin/padding to zero, set the board to `100vw × 100vh`, use `overflow: hidden`. Critically, use `width: 100%` rather than `width: 100vw` to avoid the scrollbar-width issue where `100vw` includes scrollbar width and causes horizontal overflow.

**When to use:** Whenever the board must fill the entire browser window with no scrolling.

**Example:**
```css
/* Source: MDN position docs + Smashing Magazine scrollbar research */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  width: 100%;
  height: 100vh;
  overflow: hidden;
}

#board {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow: hidden;
  background-color: #2a1f14; /* deep dark cork brown */
}
```

### Pattern 2: Cork Texture via Inline SVG feTurbulence

**What:** Embed an SVG `feTurbulence` filter as a URL-encoded data URL in `background-image`. Layer it over the board's solid background color. The SVG is transparent except for the noise pattern, so the background color shows through, tinted by the grain.

**When to use:** When CSS-only grain/noise texture is required without image assets.

**Example:**
```css
/* Source: freecodecamp.org/news/grainy-css-backgrounds-using-svg-filters */
#board {
  background-color: #2a1f14;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 250 250' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)'/%3E%3C/svg%3E");
  background-size: 250px 250px; /* tile the grain pattern */
}
```

Key parameters:
- `baseFrequency='0.75'` — medium grain size; raise toward `1.2` for finer grain, lower toward `0.5` for coarser
- `numOctaves='4'` — more octaves = richer organic texture; `3–4` is the sweet spot for cork
- `stitchTiles='stitch'` — makes tiles seamless

### Pattern 3: Vignette Overlay via ::after Pseudo-Element

**What:** A `position: absolute` `::after` on the board container, full-size, using `radial-gradient(circle, transparent 50%, rgba(0,0,0,0.7) 150%)` to darken edges and corners.

**When to use:** Board atmosphere — cinematic dark-edge vignette without a second DOM element.

**Example:**
```css
/* Source: una.im/vignettes — radial gradient pseudo-element approach */
#board::after {
  content: "";
  position: absolute;
  inset: 0;               /* shorthand for top:0; right:0; bottom:0; left:0 */
  background: radial-gradient(
    circle at center,
    transparent 40%,
    rgba(0, 0, 0, 0.6) 120%
  );
  pointer-events: none;   /* CRITICAL: allows click-through to cards below */
  z-index: 10;            /* above board texture, below cards */
}
```

**CRITICAL:** `pointer-events: none` is mandatory. Without it, the vignette overlay intercepts all mouse events — dragging (Phase 2) and clicking (Phase 4) would stop working.

### Pattern 4: Scattered Cards with Absolute Percentage Positioning

**What:** Each card uses `position: absolute` with `top` and `left` expressed as percentages of the containing board. Tilt uses inline `style` attribute for per-card rotation.

**When to use:** 5 hard-coded cards at varied positions; percentages ensure layout adapts to window size.

**Example:**
```html
<!-- Source: MDN position docs -->
<div id="board">
  <div class="card" style="top: 12%; left: 8%; transform: rotate(-3deg)">
    <span class="card-title">Suspect A</span>
  </div>
  <div class="card" style="top: 55%; left: 20%; transform: rotate(2deg)">
    <span class="card-title">Victim</span>
  </div>
  <div class="card" style="top: 20%; left: 42%; transform: rotate(-1deg)">
    <span class="card-title">Crime Scene</span>
  </div>
  <div class="card" style="top: 60%; left: 58%; transform: rotate(4deg)">
    <span class="card-title">Alibi</span>
  </div>
  <div class="card" style="top: 15%; left: 72%; transform: rotate(-2deg)">
    <span class="card-title">Witness</span>
  </div>
</div>
```

Tilt range: ±2–5° per CONTEXT.md decision. Values above are representative; exact positions are Claude's discretion.

### Pattern 5: Thumbtack Pin via ::before Pseudo-Element

**What:** `::before` on `.card` creates a colored circle at the top-center, simulating a round thumbtack head pinned through the card.

**When to use:** All `.card` elements need a pin at top center.

**Example:**
```css
/* Source: css-shape.com/pin — adapted for round head only */
.card {
  position: absolute;
}

.card::before {
  content: "";
  position: absolute;
  top: -10px;              /* sit above the card top edge */
  left: 50%;
  transform: translateX(-50%);
  width: 16px;
  height: 16px;
  border-radius: 50%;      /* circle = thumbtack head */
  background: radial-gradient(circle at 35% 35%, #ff6b6b, #c0392b);
  box-shadow:
    0 2px 4px rgba(0,0,0,0.5),
    inset 0 1px 2px rgba(255,255,255,0.3); /* slight highlight for 3D */
  z-index: 2;
}
```

### Pattern 6: Layered Box-Shadow for Physical Lift

**What:** Multiple `box-shadow` layers with increasing offset and blur simulate both a contact shadow (tight, dark) and an ambient shadow (soft, wide). Match shadow hue to board background for realism.

**When to use:** Cards must look physically raised off the cork board.

**Example:**
```css
/* Source: joshwcomeau.com/css/designing-shadows */
.card {
  box-shadow:
    2px 4px 4px rgba(0, 0, 0, 0.35),
    4px 8px 8px rgba(0, 0, 0, 0.25),
    8px 16px 16px rgba(0, 0, 0, 0.18),
    12px 24px 32px rgba(0, 0, 0, 0.12);
}
```

### Anti-Patterns to Avoid

- **Using `width: 100vw` on the board:** Causes horizontal scrollbar in browsers with classic scrollbars because `100vw` includes scrollbar width. Use `width: 100%` instead.
- **Forgetting `pointer-events: none` on vignette overlay:** The `::after` pseudo-element sits on top of cards. Without this, Phase 2 drag and Phase 4 click events will be captured by the overlay and never reach the cards.
- **Using `::before` for both the pin AND the title:** A card can only have one `::before`. Title must go in a DOM element (`<span class="card-title">`), not generated content.
- **Setting `z-index` without `position` on cards:** `z-index` has no effect on `position: static` elements. Cards are `position: absolute`, so this is fine — but be aware when stacking overlay layers.
- **Hardcoding pixel positions for cards:** Pixel positions break at different window sizes. Use `top`/`left` as percentages so positions remain proportional.
- **Setting `overflow: visible` on `#board`:** Absolute-positioned cards that partially fall outside the board boundaries will cause scrollbars to appear. `overflow: hidden` on `#board` clips them.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Grain/noise texture | Custom repeating CSS gradient hacks | SVG `feTurbulence` via inline data URL | Gradient hacks produce mechanical patterns; `feTurbulence` produces organic noise. All modern browsers support it. |
| Vignette effect | Multiple box-shadows on board | `radial-gradient` pseudo-element | `radial-gradient` is resolution-independent; box-shadows on large elements are expensive to render |
| Typewriter font | Custom font download | `font-family: 'Courier New', Courier, monospace` | Web-safe font stack; zero network requests; universally available |

**Key insight:** For a static layout phase with no JavaScript, there is nothing to "hand-roll" in terms of logic. The risk is over-engineering the visual effects when CSS has native solutions for every texture and shadow needed here.

---

## Common Pitfalls

### Pitfall 1: Scrollbars Appearing on Full-Viewport Board
**What goes wrong:** The board appears slightly wider or taller than the window, creating horizontal or vertical scrollbars.
**Why it happens:** `width: 100vw` includes scrollbar width (15–17px in Windows Chrome). Unresolved `body` margin (default 8px in browsers). Overflow content from absolutely-positioned cards with negative offset.
**How to avoid:** Reset all browser defaults with a `*, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }` block. Use `width: 100%` not `100vw` for the board. Set `overflow: hidden` on both `body` and `#board`.
**Warning signs:** Any scrollbar visible in the success criteria visual check. Board background does not reach window edges.

### Pitfall 2: Vignette Overlay Blocking Card Interaction
**What goes wrong:** In Phase 1 this is invisible (no interaction), but it will silently break Phase 2 (drag) and Phase 4 (click-to-open). The overlay intercepts all mouse events.
**Why it happens:** The `::after` pseudo-element on `#board` sits above cards in z-index. Mouse events hit the overlay first and stop.
**How to avoid:** Always set `pointer-events: none` on all overlay pseudo-elements that sit above interactive elements.
**Warning signs:** In Phase 2, cards cannot be grabbed despite correct JavaScript. In Phase 4, clicking cards does nothing.

### Pitfall 3: Grain Texture Looks Banded or Mechanical
**What goes wrong:** The background has visible repeating bands or a regular pattern rather than organic grain.
**Why it happens:** Wrong `feTurbulence` parameters. `type='turbulence'` creates banded Perlin noise. `baseFrequency` set too low creates coarse unnatural blobs.
**How to avoid:** Use `type='fractalNoise'` (not `turbulence`). Set `baseFrequency` between `0.65` and `0.9` for cork-scale grain. Use `numOctaves='3'` or `'4'`.
**Warning signs:** Background looks like tie-dye or fingerprint ridges instead of natural surface grain.

### Pitfall 4: Cards Look Like Web UI, Not Physical Objects
**What goes wrong:** Cards look like website tiles or flat rectangles, not pinned notecard paper.
**Why it happens:** Insufficient shadow depth, wrong background color (pure white instead of cream), missing tilt, pin pseudo-element too small to be visible.
**How to avoid:** Use layered box-shadow with at least 3 layers. Background color `#fdf6e3` or similar warm cream. Tilt at ±2–5°. Pin `::before` should be at least 14px diameter with a red radial-gradient. Shadow offset should be obvious at normal viewing distance (8px+ at minimum).
**Warning signs:** Cards look flat or "digital." Pin is not visible or looks like an artifact.

### Pitfall 5: Grain Texture Rendered Too Dark or Too Light
**What goes wrong:** The noise overlay either bleaches the dark background to grey or is invisible.
**Why it happens:** The SVG noise layer's blend mode and opacity need tuning. Without a mix-blend-mode, a white-dominant SVG noise pattern on a dark background produces grey wash.
**How to avoid:** Test with `opacity: 0.08–0.15` on the grain overlay. Use `mix-blend-mode: soft-light` on the grain `::before` to interact naturally with the dark background. Or simply rely on the base `background-image` SVG overlay on the solid color — the dark background shows through transparent regions in the noise naturally.
**Warning signs:** Board background looks uniform grey rather than dark brown with texture.

---

## Code Examples

Verified patterns from official and authoritative sources:

### Complete Board CSS
```css
/* Source: MDN (position, overflow, box-sizing), freecodecamp.org (feTurbulence), una.im (vignette) */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  width: 100%;
  height: 100vh;
  overflow: hidden;
}

#board {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow: hidden;
  background-color: #2a1f14;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)' opacity='0.12'/%3E%3C/svg%3E");
  background-size: 200px 200px;
}

/* Vignette overlay */
#board::after {
  content: "";
  position: absolute;
  inset: 0;
  background: radial-gradient(
    circle at center,
    transparent 40%,
    rgba(0, 0, 0, 0.65) 120%
  );
  pointer-events: none;
  z-index: 10;
}
```

### Complete Card CSS
```css
/* Source: joshwcomeau.com (shadows), css-tricks.com (pseudo-elements), MDN (transform) */
.card {
  position: absolute;
  width: 160px;
  height: 100px;        /* 8:5 ratio — index card proportion */
  background: #fdf6e3;  /* warm cream — not pure white */
  border-radius: 2px;
  padding: 28px 12px 10px; /* top padding leaves room for pin */

  /* Layered physical lift shadow */
  box-shadow:
    2px 4px 4px rgba(0, 0, 0, 0.35),
    4px 8px 10px rgba(0, 0, 0, 0.25),
    8px 16px 20px rgba(0, 0, 0, 0.18),
    12px 28px 36px rgba(0, 0, 0, 0.10);

  z-index: 5;            /* above board texture/grain; below vignette */
}

/* Thumbtack pin */
.card::before {
  content: "";
  position: absolute;
  top: -8px;
  left: 50%;
  transform: translateX(-50%);
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: radial-gradient(circle at 35% 35%, #e74c3c, #922b21);
  box-shadow:
    0 2px 4px rgba(0, 0, 0, 0.5),
    inset 0 1px 2px rgba(255, 255, 255, 0.25);
  z-index: 6;
}

/* Card title label */
.card-title {
  display: block;
  text-align: center;
  font-family: 'Courier New', Courier, monospace;
  font-size: 13px;
  font-weight: bold;
  color: #1a1a1a;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
```

### Complete HTML Structure
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Investigation Canvas</title>
  <style>
    /* ... CSS above ... */
  </style>
</head>
<body>
  <div id="board">
    <div class="card" style="top: 12%; left: 8%; transform: rotate(-3deg)">
      <span class="card-title">Suspect A</span>
    </div>
    <div class="card" style="top: 55%; left: 18%; transform: rotate(2deg)">
      <span class="card-title">Victim</span>
    </div>
    <div class="card" style="top: 20%; left: 42%; transform: rotate(-1deg)">
      <span class="card-title">Crime Scene</span>
    </div>
    <div class="card" style="top: 58%; left: 60%; transform: rotate(4deg)">
      <span class="card-title">Alibi</span>
    </div>
    <div class="card" style="top: 15%; left: 74%; transform: rotate(-2deg)">
      <span class="card-title">Witness</span>
    </div>
  </div>
</body>
</html>
```

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| External texture image (cork.jpg) | Inline SVG `feTurbulence` via data URL | ~2018 (SVG filters widely adopted) | Zero network requests, infinite resolution, no asset management |
| `width: 100vw` for full-width layout | `width: 100%` on body/container | ~2020 (scrollbar issue well-documented) | Eliminates horizontal scrollbar in Windows Chrome/Firefox |
| Single flat `box-shadow` | Layered `box-shadow` with multiple values | ~2019 (popularized by Josh Comeau) | Dramatically more realistic physical depth |
| Separate `.pin` DOM element | `::before` pseudo-element | CSS2.1 baseline | Reduces DOM nodes; pin is purely decorative |
| `position: absolute` with `px` values | `position: absolute` with `%` values | Responsive design era | Layout adapts to different viewport sizes |

**Deprecated/outdated:**
- Using `filter: drop-shadow()` for card lift: Less predictable than `box-shadow`; interacts unexpectedly with transforms; stick to `box-shadow`.
- Pure CSS gradient noise hacks (`repeating-conic-gradient` exploits): Fragile, browser-dependent, produce mechanical patterns. Use `feTurbulence` instead.

---

## Open Questions

1. **Grain opacity vs. mix-blend-mode**
   - What we know: Setting a low `opacity` on the SVG noise rect or on a `::before` layer both work to control grain visibility.
   - What's unclear: Which approach (opacity on the SVG rect vs. `mix-blend-mode: soft-light` on a pseudo-element) produces the most convincing cork texture at the specified dark background color `#2a1f14`.
   - Recommendation: The executor should implement both techniques and visually evaluate. The opacity-in-SVG approach (shown in code examples above) is simpler to start with.

2. **Card z-index vs. vignette z-index**
   - What we know: Vignette `::after` must sit above board grain but below cards so the vignette dims the board but not the cards.
   - What's unclear: Whether the cinematic effect looks better with vignette above cards (slightly darkening card edges at board perimeter) or strictly below.
   - Recommendation: Start with vignette below cards (`z-index: 10` on vignette, `z-index: 20` on cards). The user/executor can test both visually and decide — this falls under Claude's discretion per CONTEXT.md.

---

## Sources

### Primary (HIGH confidence)
- https://www.freecodecamp.org/news/grainy-css-backgrounds-using-svg-filters/ — feTurbulence inline SVG data URL technique, exact CSS code verified
- https://css-tricks.com/grainy-gradients/ — grain texture layering approach, SVG filter structure
- https://frontendmasters.com/blog/grainy-gradients/ — SVG displacement map grain technique
- https://una.im/vignettes/ — radial-gradient vignette pseudo-element approach, exact CSS code verified
- https://www.joshwcomeau.com/css/designing-shadows/ — layered box-shadow technique, exact CSS values
- https://css-shape.com/pin/ — CSS pin/thumbtack shape via pseudo-element
- https://www.smashingmagazine.com/2023/12/new-css-viewport-units-not-solve-classic-scrollbar-problem/ — 100vw scrollbar issue documented
- https://www.cssfontstack.com/Courier-New — Courier New web-safe font stack, cross-browser availability

### Secondary (MEDIUM confidence)
- https://chenhuijing.com/blog/css-card-shadow-effects/ — box-shadow layering for card lift (consistent with Josh Comeau primary source)
- https://www.bstefanski.com/blog/noisygrainy-backgrounds-and-gradients-in-css — additional grain technique reference
- https://tobiasahlin.com/blog/layered-smooth-box-shadows/ — layered shadow principles (consistent with primary source)

### Tertiary (LOW confidence)
- None — all key claims are backed by primary or secondary sources.

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — no third-party libraries needed; all CSS3 features used are in the baseline for all modern browsers
- Architecture: HIGH — single HTML file with embedded styles is unambiguously correct for the project constraints
- Pitfalls: HIGH — scrollbar issue (100vw), pointer-events on overlay, and feTurbulence parameters are all well-documented in authoritative sources
- Code examples: MEDIUM — examples are correct in structure but exact values (grain opacity, shadow spread, card dimensions, vignette radius) will need visual tuning during execution

**Research date:** 2026-02-24
**Valid until:** 2026-04-24 (CSS/HTML specs are extremely stable; these techniques have been standard for 4+ years)
