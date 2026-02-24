# Technology Stack

**Project:** Investigation Canvas
**Researched:** 2026-02-24
**Constraint:** Vanilla HTML + CSS + JS only — no npm, no frameworks, no external libraries

---

## Recommended Stack

### Interaction Layer (Drag and Drop)

| Technology | API Version | Purpose | Why |
|------------|-------------|---------|-----|
| Pointer Events API | Baseline widely available (July 2020) | Card dragging on the board | Unified mouse/touch/pen API, enables `setPointerCapture` for reliable drag outside element bounds |
| `element.setPointerCapture(pointerId)` | Same | Lock event target during drag | Prevents losing drag when pointer moves fast outside card boundary |
| CSS `position: absolute` + `left`/`top` | Universal | Card placement on board | Explicit coordinates are readable for SVG line computation; simpler than transforms |

**Do NOT use:** HTML Drag and Drop API (`draggable` attribute, `dragstart`/`drop`). Designed for inter-app data transfer. Limitations: data must serialize to strings, drag image is a ghost (not the real card), no real-time coordinates during drag, conflicts with text selection.

**Do NOT use:** Raw `mousemove`/`mousedown`/`mouseup`. Pointer Events supersede these. No advantages to using mouse events for this use case.

---

### Connection Layer (Red String Lines)

| Technology | Purpose | Why |
|------------|---------|-----|
| Inline SVG overlay (`position: fixed; inset: 0; pointer-events: none`) | Red string connections between cards | SVG lives in same coordinate space; lines are scalable and crisp |
| `<line>` element with `setAttribute` | Straight string connections | Simplest to update dynamically; one `setAttribute` call per endpoint per drag frame |
| `<path>` with quadratic bezier (`Q` command) | Optional: sagging string effect | Single control point below midpoint simulates gravity droop |
| `document.createElementNS("http://www.w3.org/2000/svg", "line")` | Dynamic line creation | Required for SVG elements — `createElement` does not work for SVG namespace |

**SVG Overlay:**
```html
<svg id="connections"
     style="position: fixed; inset: 0; width: 100%; height: 100%;
            pointer-events: none; z-index: 5;">
</svg>
```

No `viewBox` needed — coordinate space matches CSS pixels via `getBoundingClientRect()` directly.

**Do NOT use:** Canvas for line rendering. Canvas is raster; lines blur at browser zoom. SVG is vector, individual lines are DOM elements (easy add/remove, no full-redraw needed).

---

### Drawing Layer (Free-hand Annotation)

| Technology | Purpose | Why |
|------------|---------|-----|
| `<canvas>` with `getContext('2d')` | Free-hand drawing inside expanded card view | Native API, no dependencies; Baseline July 2015 |
| `CanvasRenderingContext2D` path methods | Stroke rendering | `beginPath()`, `moveTo()`, `lineTo()`, `stroke()` |
| Pointer Events on canvas | Capture drawing input | Same API as drag; consistent event model |
| `e.offsetX` / `e.offsetY` | Canvas-relative coordinates | Pre-computed by browser to canvas local space — no subtraction needed |

**devicePixelRatio Scaling (required for Retina displays):**
```javascript
const dpr = window.devicePixelRatio || 1;
canvas.width = canvas.offsetWidth * dpr;
canvas.height = canvas.offsetHeight * dpr;
canvas.style.width = canvas.offsetWidth + 'px';
canvas.style.height = canvas.offsetHeight + 'px';
ctx.scale(dpr, dpr);
// After scaling, use e.offsetX/e.offsetY directly — they are already CSS pixels
```

---

### Modal / Expanded Card View

| Technology | Purpose | Why |
|------------|---------|-----|
| `<dialog>` element | Expanded evidence detail overlay | Native modal: top layer (no z-index conflicts), focus trapping, Esc key built-in, `::backdrop` pseudo-element |
| `dialog.showModal()` | Open modal | Focuses first focusable child, blocks background |
| `dialog.close()` | Close modal | Programmatic close |
| `::backdrop` CSS pseudo-element | Darkened overlay behind dialog | Native — no div overlay needed |

**Baseline:** Widely available since March 2022. Safe for all modern desktop browsers.

**Do NOT use:** Custom div modals with `z-index`. `<dialog>` handles focus trapping, top-layer placement, and keyboard dismissal natively.

---

### Visual Aesthetics (CSS)

| Technique | Purpose | Details |
|-----------|---------|---------|
| CSS `background-image` with layered gradients | Cork board texture | No image files; `radial-gradient` layers simulate cork grain |
| `box-shadow` (multi-layer) | Card depth and pin effect | Layered shadows create physical elevation |
| `cursor: grab` / `cursor: grabbing` | Drag affordance | Baseline December 2021 |
| `cursor: crosshair` | Connect mode affordance | Same baseline |
| CSS Custom Properties (`--var`) | Theme tokens | Board color, card color, red string color at `:root` |
| `user-select: none` on cards | Prevent text selection during drag | Add from day one |

---

## Alternatives Considered

| Category | Recommended | Alternative | Why Not |
|----------|-------------|-------------|---------|
| Drag events | Pointer Events + `setPointerCapture` | HTML Drag and Drop API | No real-time position, ghost image only, serialization required |
| Drag events | Pointer Events | Mouse Events | Pointer Events are the modern successor |
| String connections | SVG `<line>` | Canvas 2D | SVG is vector, DOM nodes, no full-redraw on change |
| Modal | `<dialog>` element | Custom div + z-index | Dialog handles focus, top-layer, Esc natively |
| Drawing | Canvas 2D | SVG path recording | Canvas simpler for freehand; SVG path recording adds complexity |
| Board texture | CSS gradients | External PNG | Zero dependencies, resolution-independent |
| Card positioning | `left`/`top` (absolute) | `transform: translate()` | Values directly readable as numbers; simpler SVG coordinate math |

---

## File Structure

```
investigation-canvas/
  index.html    # Markup: board, SVG overlay, card elements, dialog
  style.css     # All styles: board, cards, connections, dialog, pin aesthetic
  app.js        # All behavior: drag, connect mode, SVG updates, canvas drawing
```

---

## Browser Support

| API | Baseline Status | Safe? |
|-----|----------------|-------|
| Pointer Events + `setPointerCapture` | Widely available (July 2020) | Yes |
| SVG `<line>`, `createElementNS` | Widely available (legacy) | Yes |
| Canvas 2D `CanvasRenderingContext2D` | Widely available (July 2015) | Yes |
| `<dialog>` + `showModal()` | Widely available (March 2022) | Yes |
| CSS `cursor: grab/grabbing` | Widely available (December 2021) | Yes |
| `getBoundingClientRect()` | Widely available (legacy) | Yes |

---

## Running the Project

No installation required. Zero dependencies.

```bash
open index.html
# or
python3 -m http.server 8080
```

---

## Sources

- MDN Pointer Events API — `setPointerCapture`, `pointerdown/move/up`
- MDN HTML Drag and Drop API — documented limitations
- MDN SVG `<line>` element and `createElementNS`
- MDN `CanvasRenderingContext2D` — path methods, `devicePixelRatio` scaling
- MDN `HTMLDialogElement` — `showModal()`, Escape handling, top-layer
- MDN `getBoundingClientRect()` — viewport-relative coordinates
- MDN CSS `cursor` property — `grab`, `grabbing`, `crosshair` values
