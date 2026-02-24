# Domain Pitfalls

**Domain:** Vanilla JS interactive board — drag-and-drop cards, SVG connections, Canvas drawing
**Researched:** 2026-02-24
**Confidence:** HIGH (well-established failure modes from browser API specification behavior)

---

## Critical Pitfalls

Mistakes that cause rewrites, broken interactions, or the "it almost works" trap.

---

### Pitfall 1: SVG Overlay Not Covering Full Viewport / Wrong Stacking Order

**What goes wrong:** The SVG element that renders connection lines clips, occludes click events on cards, or doesn't resize with the viewport. Lines appear cut off or cards stop responding to mouse events because the SVG intercepts them.

**Why it happens:** The `<svg>` is placed as a regular block element instead of position-absolute covering the full viewport. The `pointer-events: none` rule is forgotten, so the SVG eats mouse events intended for cards beneath it.

**Consequences:** Cards become un-draggable after the SVG is added. Lines get clipped. Window resize breaks line rendering. Full structural refactor required.

**Prevention:**
- Set SVG: `position: fixed; inset: 0; width: 100%; height: 100%; pointer-events: none;` as a child of the board container.
- Only restore `pointer-events: auto` on individual `<line>` elements that need click interaction.
- Set SVG `overflow: visible` so connections near the edge don't clip.

**Detection:** Drop a card while a line is connected — if dragging stops working, check `pointer-events` on the SVG.

**Phase:** Connection system implementation.

---

### Pitfall 2: SVG Line Coordinates in the Wrong Coordinate Space

**What goes wrong:** SVG `<line>` x1/y1/x2/y2 are calculated from `getBoundingClientRect()` (viewport-relative) but the SVG has a different coordinate origin. Lines point to wrong positions.

**Why it happens:** `getBoundingClientRect()` returns viewport coordinates. If the SVG container is not at `(0, 0)` in the document, there is an offset mismatch.

**Consequences:** Lines point to wrong positions — slightly off initially, wildly wrong after any scrolling or layout change.

**Prevention:**
- Anchor the SVG to `position: fixed` at `inset: 0`.
- Use `getBoundingClientRect()` for both cards — since the SVG covers the full viewport at the same coordinate origin, no offset subtraction is needed.

**Detection:** Place a visible dot at the calculated coordinate. If it doesn't align with the card center, coordinate spaces are mismatched.

**Phase:** Connection system implementation.

---

### Pitfall 3: Drag Offset Not Captured at Pointerdown — Cards Jump on Drag Start

**What goes wrong:** When the user clicks a card and starts dragging, the card immediately snaps so its top-left corner sits under the cursor, not where the user grabbed it.

**Why it happens:** The drag handler sets position to `(event.clientX, event.clientY)` directly, ignoring the grab offset within the card.

**Consequences:** The interaction feels broken. Users lose context of where they grabbed. Cards jump unexpectedly.

**Prevention:**
- On `pointerdown`: capture `offsetX = event.clientX - card.offsetLeft` and `offsetY = event.clientY - card.offsetTop`.
- On `pointermove`: set `card.style.left = (event.clientX - offsetX) + 'px'` and `card.style.top = (event.clientY - offsetY) + 'px'`.

**Detection:** Click a card near its bottom-right corner and drag. If the card jumps to cursor, offset is missing.

**Phase:** Card drag implementation.

---

### Pitfall 4: Not Using setPointerCapture — Fast Drag Breaks

**What goes wrong:** If the user moves the mouse faster than event firing, the cursor leaves the card. Dragging stops. `pointerup` never fires on the card, leaving the card in permanent "dragging" state.

**Why it happens:** Without `setPointerCapture`, pointer events are dispatched to whatever element is under the cursor. Move fast enough and the pointer leaves the card.

**Consequences:** Cards get stuck mid-drag. Fast movement breaks dragging. Ghost-drag state where releasing the pointer has no effect.

**Prevention:**
- On `pointerdown`, call `card.setPointerCapture(e.pointerId)`.
- This routes all subsequent pointer events to the card regardless of cursor position.
- On `pointerup`, call `card.releasePointerCapture(e.pointerId)`.

**Detection:** Drag a card quickly in a wide arc. If it detaches, `setPointerCapture` is missing.

**Phase:** Card drag implementation.

---

### Pitfall 5: Canvas Drawing Offset on High-DPI Displays (devicePixelRatio)

**What goes wrong:** On Retina/HiDPI displays, freehand strokes appear offset from the cursor — typically at half the correct position. Drawing looks blurry and misaligned.

**Why it happens:** CSS size of the canvas differs from the canvas attribute resolution. `devicePixelRatio = 2` on Retina. The canvas attribute is set equal to CSS pixels, so it renders at half resolution.

**Consequences:** Strokes drawn 50% offset from cursor. Drawing feels broken. Fixing it post-implementation requires clearing all existing strokes.

**Prevention:**
- On canvas init: `canvas.width = canvas.offsetWidth * devicePixelRatio; canvas.height = canvas.offsetHeight * devicePixelRatio;`
- Lock CSS size: `canvas.style.width = canvas.offsetWidth + 'px'; canvas.style.height = canvas.offsetHeight + 'px';`
- Scale context: `ctx.scale(devicePixelRatio, devicePixelRatio);`
- Use `e.offsetX`/`e.offsetY` for coordinates — they are canvas-relative CSS pixels, which the scaled context handles correctly.

**Detection:** On a Retina display, draw a diagonal line following the cursor. If the line lags at half distance, devicePixelRatio is not handled.

**Phase:** Canvas drawing implementation — must be handled at initialization, not patchable later.

---

### Pitfall 6: Canvas Mouse Coordinates Using clientX/clientY Directly

**What goes wrong:** Freehand strokes are drawn relative to viewport origin, not canvas origin. On a canvas inside a modal (not at viewport `0,0`), all drawing is offset.

**Prevention:**
- Use `e.offsetX`/`e.offsetY` (canvas-relative) instead of `e.clientX`/`e.clientY` (viewport-relative).
- Or compute: `const rect = canvas.getBoundingClientRect(); const x = e.clientX - rect.left;`

**Detection:** Open the expanded view and draw a dot. If it appears offset from the cursor, this pitfall is present.

**Phase:** Canvas drawing implementation.

---

## Moderate Pitfalls

---

### Pitfall 7: SVG Lines Not Updating During Card Drag

**What goes wrong:** SVG connection lines are rendered once on creation but don't update as connected cards are dragged. Lines stay at their original position.

**Prevention:**
- On every `pointermove` during drag, after updating card position, iterate all connections involving the dragged card and update `<line>` x1/y1/x2/y2 attributes.
- Maintain: `connections = [{cardA, cardB, lineElement}]`. Update is O(n) scan per drag event.

**Detection:** Create a connection, then drag one of the connected cards. If the line doesn't follow, this pitfall is present.

**Phase:** Connection + drag integration.

---

### Pitfall 8: Z-Index Chaos — Dragged Cards Appearing Under Other Elements

**What goes wrong:** A card being dragged appears behind other cards, the SVG overlay, or the expanded modal.

**Prevention:**
- Define a z-index layer system from the start:
  - Layer 0: Board background
  - Layer 1: SVG connection overlay (`pointer-events: none`)
  - Layer 2: Cards at rest (`z-index: 10`)
  - Layer 3: Active/dragged card (`z-index: 100`)
  - Layer 4: Expanded detail modal (`z-index: 1000`)
- On `pointerdown` for drag: set `z-index: 100`. On `pointerup`: return to `10`.

**Detection:** Drag one card over another. If the dragged card appears under, z-index is missing.

**Phase:** Card drag implementation — establish layer system before writing any z-index values.

---

### Pitfall 9: Connect Mode State Leaking Between Interactions

**What goes wrong:** After using connect mode to link two cards, the app remains in "first card selected" state. No way to cancel.

**Prevention:**
- Model explicitly: `connectState = { active: false, firstCard: null }`.
- Provide cancel path: press Escape or click the same card again.
- On successful connection: immediately reset state.
- Prevent card dragging while connect mode is active.

**Detection:** Enable connect mode, click one card, then press Escape. Is the first card still highlighted? If yes, state is leaking.

**Phase:** Connection mode implementation.

---

### Pitfall 10: Expanded Card Modal With No Dismiss Path

**What goes wrong:** The expanded detail view opens but Escape and click-outside don't work.

**Prevention:**
- Use `<dialog>` element — Escape key is built-in.
- Add a visible close button.
- Add click listener on the `::backdrop` / overlay to dismiss.

**Detection:** Open a card, then try to close it with Escape. If it doesn't work, dismiss is incomplete.

**Phase:** Expanded detail view implementation.

---

### Pitfall 11: Canvas State Lost When Modal is Closed and Reopened

**What goes wrong:** User draws on a card in expanded view, closes it, reopens it — all drawings are gone.

**Why it happens:** The canvas element is destroyed and recreated with the modal DOM. Canvas content is pixel data in memory — it does not survive DOM re-creation.

**Prevention:**
- Never destroy and recreate the canvas element. Hide/show the modal instead.
- Or: serialize on close via `canvas.toDataURL()` and restore on reopen via `ctx.drawImage()`.
- Store drawing state per card ID in JavaScript state.

**Detection:** Draw on a card, close it, reopen it. If the drawing is gone, canvas state is not persisted.

**Phase:** Expanded detail view + canvas integration.

---

## Minor Pitfalls

---

### Pitfall 12: Text Selection Cursor During Card Drag

**Prevention:** Add `user-select: none` to card CSS. Toggle `cursor: grabbing` during drag via CSS class.

**Phase:** Card CSS — add from day one.

---

### Pitfall 13: Connection Line Drawn to Card Corner, Not Card Center

**Prevention:** Use center: `cx = card.offsetLeft + card.offsetWidth / 2`, `cy = card.offsetTop + card.offsetHeight / 2`.

**Phase:** SVG connection implementation.

---

### Pitfall 14: Freehand Drawing Continues After Pointer Released Outside Canvas

**Prevention:**
- Check `e.buttons === 1` at the start of every `pointermove` handler as a guard.
- Or: attach `pointerup` to `document` during drawing.

**Phase:** Canvas drawing implementation.

---

### Pitfall 15: No Visual Feedback for Connect Mode Active State

**Prevention:**
- Toggle CSS class on board to change cursor to `crosshair`.
- Highlight the first selected card visually.
- Show a visible indicator that connect mode is active.

**Phase:** Connection mode UI.

---

## Phase-Specific Warnings

| Phase Topic | Likely Pitfall | Mitigation |
|-------------|---------------|------------|
| Card drag | Grab offset not captured (#3) | Capture `offsetX/offsetY` on `pointerdown` |
| Card drag | No pointer capture (#4) | `setPointerCapture` on `pointerdown` |
| Card drag | Text selection during drag (#12) | `user-select: none` CSS from day one |
| Card drag | Z-index on active drag (#8) | Define z-index layer system before any implementation |
| SVG connections | SVG intercepts mouse events (#1) | `pointer-events: none` on SVG layer |
| SVG connections | Coordinate space mismatch (#2) | SVG at `position: fixed; inset: 0` |
| SVG connections | Line to card corner not center (#13) | Use `offsetLeft + width/2` formula |
| SVG connections + drag | Lines don't follow dragged cards (#7) | Update line endpoints inside drag `pointermove` handler |
| Connect mode | State leaking between interactions (#9) | Explicit state machine with cancel path |
| Canvas drawing | devicePixelRatio blurriness (#5) | Scale canvas at init — not patchable later |
| Canvas drawing | Wrong coordinates (#6) | Use `e.offsetX`/`e.offsetY` |
| Canvas drawing | Drawing after pointer up outside (#14) | Check `e.buttons === 1` guard |
| Expanded modal | Canvas wiped on reopen (#11) | Persist canvas, don't destroy/recreate |
| Expanded modal | No dismiss path (#10) | Use `<dialog>` + Escape key built-in |

---

## Sources

- MDN Web Docs — `PointerEvent`, `setPointerCapture`, `getBoundingClientRect()`, `HTMLCanvasElement`, `devicePixelRatio`
- MDN Web Docs — SVG `pointer-events` attribute behavior
- HTML Canvas Specification — Coordinate system and `devicePixelRatio` scaling behavior
