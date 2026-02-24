# Architecture Patterns

**Domain:** Vanilla JS interactive board — drag-and-drop, SVG connections, Canvas drawing
**Researched:** 2026-02-24
**Confidence:** HIGH

---

## Component Overview

```
┌────────────────────────────────────────────────────────────┐
│  #board  (position: relative, 100vw × 100vh, overflow: hidden) │
│                                                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  #connections SVG (position: fixed, inset: 0,        │  │
│  │                    pointer-events: none, z-index: 5) │  │
│  │                                                      │  │
│  │   <line id="conn-1-2" ... />                         │  │
│  │   <line id="conn-3-4" ... />                         │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                            │
│  .card  (position: absolute, z-index: 10)                  │
│  .card  (position: absolute, z-index: 10)                  │
│  .card  (position: absolute, z-index: 10)                  │
│  .card  (active/dragging: z-index: 100)                    │
│                                                            │
└────────────────────────────────────────────────────────────┘

<dialog id="detail-modal"> (position: fixed, top layer, z-index: 1000)
  ┌────────────────────────────────────────────────┐
  │  .modal-card-title                             │
  │  <canvas id="drawing-canvas">                  │
  │  <textarea id="annotation-text">               │
  │  <button id="close-modal">                     │
  └────────────────────────────────────────────────┘
```

---

## Component Boundaries

### Board (`#board`)
- `position: relative; width: 100vw; height: 100vh; overflow: hidden`
- Parent of all cards and the SVG overlay
- Handles no events directly — children manage their own interactions
- Cork/felt CSS background applied here

### SVG Connections Layer (`#connections`)
- `position: fixed; inset: 0; width: 100%; height: 100%; pointer-events: none; z-index: 5`
- Single `<svg>` element, always full-viewport
- Children: one `<line>` element per connection
- Never interacts with user input — all events pass through to cards below
- Updated by the drag controller and connect controller

### Evidence Cards (`.card`)
- `position: absolute` within `#board`
- `z-index: 10` at rest; `z-index: 100` while dragging
- Each card has: pin pseudo-element (CSS `::before`), title area, card body
- Manages: pointerdown (initiate drag OR connect mode selection)
- CSS `user-select: none; cursor: grab`

### Detail Modal (`<dialog>`)
- Native HTML `<dialog>` element, outside `#board` in the DOM
- Opened via `dialog.showModal()` — placed in browser's top layer automatically
- Contains: card title, drawing `<canvas>`, text `<textarea>`, close button
- One modal reused for all cards (populated with the clicked card's data on open)
- Canvas state is saved/restored from per-card state on open/close

---

## Data Flow

### State Object (Single Source of Truth)

```javascript
const state = {
  cards: [
    {
      id: 'card-1',
      x: 120,          // left position in px
      y: 80,           // top position in px
      title: 'Victim: John Doe',
      annotation: '',  // textarea content, per card
      drawing: null,   // ImageData or null (from canvas.toDataURL)
    },
    // ... 4 more cards
  ],
  connections: [
    { from: 'card-1', to: 'card-3', lineEl: <SVGLineElement> },
  ],
  mode: 'default',       // 'default' | 'connect'
  connectPending: null,  // card id of first-selected card in connect mode
  activeCardId: null,    // card being inspected in modal
};
```

**Rule:** DOM position is never read back to update state. State drives DOM. On drag, update `state.cards[n].x/y` and then write to `card.style.left/top`.

---

## Controllers

### DragController

Responsibilities:
1. On `pointerdown` on a `.card`: capture grab offset, call `card.setPointerCapture(e.pointerId)`, set `z-index: 100`
2. On `pointermove` (captured): update `card.style.left/top`, update state x/y, call `ConnectionController.updateForCard(cardId)`
3. On `pointerup`: release capture, set `z-index: 10`
4. Guard: if `state.mode === 'connect'`, do NOT initiate drag on `pointerdown` (let ConnectController handle the click)
5. Guard: distinguish click from drag — if total movement < 5px, treat as click (trigger modal open)

### ConnectController

Responsibilities:
1. Toggle connect mode: update `state.mode`, change board cursor CSS class
2. On card click in connect mode:
   - If `connectPending === null`: set `connectPending = cardId`, apply highlight CSS class
   - If `connectPending === cardId`: cancel (deselect, reset `connectPending`)
   - If `connectPending !== null && !== cardId`: create connection
3. Create connection: create `<line>` in SVG, push to `state.connections`, update endpoints
4. Cancel connect mode: Escape key listener resets `state.mode` and `connectPending`

### ConnectionRenderer

Responsibilities:
1. `updateForCard(cardId)`: scan `state.connections` for entries involving `cardId`, update `<line>` x1/y1/x2/y2
2. `getCenter(cardEl)`: return `{ x: cardEl.offsetLeft + cardEl.offsetWidth/2, y: cardEl.offsetTop + cardEl.offsetHeight/2 }`
3. `addConnection(fromId, toId)`: create `<line>` element, set stroke/width/style, append to SVG, record in state

### ModalController

Responsibilities:
1. `open(cardId)`: populate modal title and textarea from state, restore canvas drawing, call `dialog.showModal()`
2. `close()`: save textarea value and canvas ImageData to state, call `dialog.close()`
3. Handle Escape via `dialog`'s native close event (fires automatically on Escape with `<dialog>`)
4. Handle close button click

### CanvasController (inside modal)

Responsibilities:
1. `init(canvas)`: set canvas `width/height` accounting for `devicePixelRatio`, scale context
2. `startDraw(e)`: `ctx.beginPath(); ctx.moveTo(e.offsetX, e.offsetY)`
3. `draw(e)`: if `drawing && e.buttons === 1`, `ctx.lineTo(e.offsetX, e.offsetY); ctx.stroke()`
4. `endDraw()`: set `drawing = false`
5. `save()`: return `canvas.toDataURL()`
6. `restore(dataURL)`: `new Image()` → `ctx.drawImage(img, 0, 0)`

---

## Suggested Build Order

```
Phase 1: Static Board Layout
  → index.html skeleton
  → CSS board (100vw × 100vh, cork background)
  → 5 hard-coded .card elements (absolute positions, pin ::before, rotation)
  → No JS yet — validate visual look

Phase 2: Card Drag
  → DragController: pointerdown/pointermove/pointerup with setPointerCapture
  → Grab offset capture
  → Z-index management
  → user-select:none, cursor:grab/grabbing
  → Click vs drag distinction (5px threshold)

Phase 3: SVG Connections
  → SVG overlay (fixed, pointer-events:none)
  → ConnectController: mode toggle, card selection, Escape cancel
  → ConnectionRenderer: create <line>, getCenter(), updateForCard()
  → Integration: DragController calls updateForCard on pointermove

Phase 4: Expanded Detail Modal
  → <dialog> element
  → ModalController: open/close, state save/restore
  → Canvas init with devicePixelRatio scaling
  → CanvasController: draw, save, restore
  → Textarea annotation: load/save

Phase 5: Polish
  → Cork board texture refinement
  → String sag (bezier curve on SVG line → path)
  → Connect mode visual feedback (cursor, card highlight, active indicator)
  → Card weathering (sepia/rotation variety)
  → Transition animations (modal open/close)
```

Each phase is independently testable and produces a visible, working result.

---

## Key Architectural Decisions

| Decision | Rationale |
|----------|-----------|
| Single `<dialog>` reused for all cards | Avoids 5 modal DOM trees; canvas init done once |
| Save canvas as `toDataURL` on modal close | Survives modal content changes; simple save/restore |
| SVG overlay at `position: fixed` | Coordinate space matches `getBoundingClientRect()` directly |
| `setPointerCapture` not `mousemove` on document | Modern, clean, correct — no need to attach/remove document listeners |
| State object as source of truth | DOM is output-only — consistent, debuggable |
| Controllers as plain JS objects/functions | No class hierarchy needed for 5-card PoC; simplest that works |

---

## File Structure

```
investigation-canvas/
  index.html    # Board, cards, SVG overlay, dialog markup
  style.css     # Board, cards, pin, connections, modal, connect-mode cursor states
  app.js        # State, DragController, ConnectController, ConnectionRenderer,
                # ModalController, CanvasController — all in one file
```

---

## Sources

- MDN Pointer Events — `setPointerCapture`, `pointermove`, `pointerup` behavior
- MDN HTMLDialogElement — `showModal()`, native Escape handling, top-layer
- MDN HTMLCanvasElement — `getContext('2d')`, `toDataURL()`, `devicePixelRatio` scaling
- MDN SVG — `pointer-events` attribute, coordinate system with `position: fixed` SVG
- MDN getBoundingClientRect — returns post-transform visual position in viewport coordinates
