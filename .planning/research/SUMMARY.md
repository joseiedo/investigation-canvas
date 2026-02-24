# Project Research Summary

**Project:** Investigation Canvas
**Domain:** Vanilla JS interactive board — detective/conspiracy wall UI for a board game
**Researched:** 2026-02-24
**Confidence:** HIGH

## Executive Summary

Investigation Canvas is a proof-of-concept detective conspiracy board UI built entirely with vanilla HTML, CSS, and JavaScript — no frameworks, no npm, no dependencies. The project replicates a well-codified real-world trope (True Detective, Homeland, A Beautiful Mind) using standard browser APIs. The technology choices are highly constrained by the "vanilla only" requirement, but this turns out to be an advantage: every required capability (drag, SVG connections, canvas drawing, modal) has a native API that is widely available in modern browsers and requires no polyfills.

The recommended approach is to build in five sequential, independently testable phases: static visual layout first, then drag, then SVG connections, then the expanded detail modal with canvas drawing, then polish. This ordering is dictated by the feature dependency chain — SVG lines depend on knowing card positions, which requires drag to be working, and the modal depends on the click-vs-drag distinction that the drag system must implement first. Skipping ahead will create integration rework at every step.

The single most important risk is implementing the SVG connection layer incorrectly: wrong coordinate space or missing `pointer-events: none` will silently break card dragging and require structural refactoring to fix. The second most important risk is the canvas `devicePixelRatio` scaling — it cannot be patched after drawing has started. Both must be handled from the first line of code that introduces those components.

---

## Key Findings

### Recommended Stack

The project uses four distinct browser technology layers, each with a clear rationale. The Pointer Events API (with `setPointerCapture`) handles all drag interactions — it supersedes mouse events, works for touch/pen/mouse uniformly, and critically prevents lost drags when the cursor moves faster than event firing. An inline SVG overlay (fixed, full-viewport, `pointer-events: none`) renders the red string connections as scalable `<line>` elements that update in real time during drag. A `<canvas>` element inside the expanded view handles freehand annotation, requiring careful `devicePixelRatio` initialization. The native `<dialog>` element handles the expanded card modal, providing focus trapping, Escape key dismissal, and top-layer placement without any z-index engineering.

**Core technologies:**
- **Pointer Events API + `setPointerCapture`:** Card drag — unified event model, prevents lost drags on fast movement
- **Inline SVG `<line>` overlay:** Red string connections — vector, per-element DOM nodes, no full redraw needed
- **Canvas 2D (`getContext('2d')`):** Freehand drawing inside expanded view — native API, requires `devicePixelRatio` scaling at init
- **Native `<dialog>` element:** Expanded card modal — focus trap, Escape key, top-layer placement, all native
- **CSS `position: absolute` + `left`/`top`:** Card positioning — values are directly readable as numbers for SVG coordinate math
- **CSS Custom Properties + layered gradients:** Visual aesthetics — zero files, resolution-independent cork texture

**Critical exclusions:** HTML Drag and Drop API (no real-time coordinates, ghost image only), `<canvas>` for connection lines (raster, no per-element control), custom div modals (manual z-index and focus management).

### Expected Features

The detective board trope is extremely well-codified across 20+ film, TV, and game references. Table stakes are non-negotiable: without cork texture, pinned cards, drag, and red string connections, the product does not read as a conspiracy board. The free-hand drawing and text annotation inside the expanded view are the primary differentiators — they transform a visual prop into an interactive artifact that players can annotate with their own theories.

**Must have (table stakes):**
- Cork/dark textured board background — sets the scene; without it, cards feel like a generic kanban
- Pinned cards with pin aesthetic and slight random rotation — establishes the physical metaphor
- Freely draggable cards — core "arranging evidence" agency
- Red string SVG connections between cards — the single most iconic element of the trope
- Strings update on drag — connection must follow the card or the illusion breaks
- Expanded view on card click — the "pick it up and examine" payoff
- Board fills full viewport — immersion requires no scrollbars or constrained areas

**Should have (differentiators):**
- Freehand drawing on individual cards inside expanded view — tactile annotation fantasy
- Text annotation field inside expanded view — theory recording per card
- Connect mode toggle (cursor changes to crosshair) — prevents accidental connections
- String sag via SVG quadratic bezier — physical authenticity detail
- Weathered/aged card aesthetic via CSS filter — noir period feel

**Defer to v2+:**
- Sound design — high effort, adds nothing to validating the board feel for a PoC
- Card face-down state — game mechanic complexity, not board feel complexity
- Multiple string colors — one color validates the feel; color system adds state management
- Sticky notes as a secondary card type — single card type is sufficient for PoC
- Dynamic add/remove of cards, persistent state, zoom/pan, mobile touch, undo/redo

### Architecture Approach

The architecture is a flat single-page application with five named controllers operating on a central state object. State is the single source of truth — the DOM is write-only output. The component structure maps precisely to the visual layers: a board container holds the SVG overlay and absolutely-positioned cards; the `<dialog>` element sits outside the board in the DOM and is placed in the browser's top layer on open. All five controllers (DragController, ConnectController, ConnectionRenderer, ModalController, CanvasController) are plain JavaScript objects or functions in a single `app.js` — no class hierarchy is needed for a 5-card PoC.

**Major components:**
1. **Board (`#board`)** — `position: relative; 100vw x 100vh; overflow: hidden`; cork background; parent of cards and SVG overlay; handles no events directly
2. **SVG Connections Layer (`#connections`)** — `position: fixed; inset: 0; pointer-events: none`; one `<line>` per connection; updated by drag and connect controllers
3. **Evidence Cards (`.card`)** — `position: absolute` within board; `z-index: 10` at rest, `100` while dragging; manages pointerdown for drag and connect mode
4. **Detail Modal (`<dialog>`)** — native top-layer element outside board; one modal reused for all cards; contains canvas, textarea, title, and close button
5. **Central State Object** — single source of truth for card positions, connections, annotations, canvas drawings, and mode state

### Critical Pitfalls

1. **SVG overlay with wrong `pointer-events` setting** — the SVG layer intercepts all mouse events and makes cards un-draggable. Set `pointer-events: none` on the `<svg>` element from the first line it is introduced. This is a structural error that requires refactoring to fix.

2. **SVG line coordinates in wrong coordinate space** — `getBoundingClientRect()` returns viewport coordinates; if the SVG is not anchored at `position: fixed; inset: 0`, line endpoints are offset. Use fixed positioning so the SVG coordinate origin matches the viewport origin exactly.

3. **Missing `setPointerCapture` on drag start** — fast pointer movement causes the pointer to leave the card element, stopping drag and leaving cards in a permanent dragging state. Call `card.setPointerCapture(e.pointerId)` on every `pointerdown` that initiates a drag.

4. **Canvas `devicePixelRatio` not handled at initialization** — on Retina/HiDPI displays, strokes appear at half the correct position and the canvas is blurry. This must be applied at canvas init — it cannot be patched after drawing has started without clearing all existing drawings.

5. **Canvas drawing coordinates using `clientX`/`clientY`** — inside a modal, the canvas is not at viewport origin. Use `e.offsetX`/`e.offsetY` (canvas-relative) or subtract `getBoundingClientRect()` from `clientX`/`clientY`.

---

## Implications for Roadmap

Based on the combined research, the architecture file's build order is the correct phase structure. Each phase is independently testable and produces a visible, working result. The dependency chain (visual -> drag -> connections -> modal -> polish) cannot be reordered without creating integration rework.

### Phase 1: Static Board Layout

**Rationale:** Visual validation before any JavaScript. Establishes the cork background, card pin aesthetic, and card rotation — the three CSS elements that create the "detective board" identity. No interaction code means no bugs to debug while validating the look.
**Delivers:** A fully styled, static board with 5 hard-coded cards positioned absolutely, pin pseudo-elements, and cork background texture.
**Addresses:** Cork board background, pinned card aesthetic, slight rotation (all table stakes from FEATURES.md)
**Avoids:** Premature complexity; wrong z-index layers before the layer system is defined (Pitfall 8)

### Phase 2: Card Drag

**Rationale:** Drag is the foundational interaction that everything else depends on. SVG connections depend on card positions being managed by the drag system. The click-vs-drag distinction (5px threshold) must be in place before the modal is built, or the two interactions will conflict.
**Delivers:** Cards draggable anywhere on the board; grab offset correct; fast drag doesn't break; cursor changes to `grabbing` during drag.
**Addresses:** Freely draggable cards (table stakes), click-vs-drag distinction needed for expanded view
**Uses:** Pointer Events API + `setPointerCapture`, CSS `user-select: none`, `cursor: grab/grabbing`
**Avoids:** Grab offset not captured (Pitfall 3), missing `setPointerCapture` (Pitfall 4), text selection during drag (Pitfall 12), z-index chaos (Pitfall 8)

### Phase 3: SVG Connections

**Rationale:** Red string connections are the single most iconic element of the conspiracy board trope. They can only be built after drag is working because connections must update on every `pointermove`. The coordinate system must be established correctly from the start or all line positions will be wrong.
**Delivers:** Connect mode toggle, click card A then card B to draw a red line, line updates in real time as connected cards are dragged.
**Addresses:** Red string connections (table stakes), string follows card on drag (table stakes), connect mode toggle (differentiator)
**Uses:** SVG `<line>` with `createElementNS`, `position: fixed; inset: 0` SVG overlay, `getBoundingClientRect()` for center coordinates
**Avoids:** SVG overlay intercepting mouse events (Pitfall 1), coordinate space mismatch (Pitfall 2), lines not updating during drag (Pitfall 7), connect mode state leaking (Pitfall 9), line connecting to corner not center (Pitfall 13)

### Phase 4: Expanded Detail Modal

**Rationale:** The expanded view is the "pick it up and examine" payoff that justifies the physical metaphor. It requires Phase 2's click-vs-drag distinction to trigger correctly. Canvas must be initialized with `devicePixelRatio` scaling at this phase — not patchable later.
**Delivers:** Card click opens `<dialog>` with title, freehand drawing canvas, and text annotation textarea; drawings and notes persist across open/close within session; Escape and close button both dismiss.
**Addresses:** Click to inspect (table stakes), freehand drawing (differentiator), text annotation (differentiator)
**Uses:** Native `<dialog>` + `showModal()`, Canvas 2D with `devicePixelRatio` scaling, `canvas.toDataURL()` for draw state persistence
**Avoids:** Canvas `devicePixelRatio` blurriness (Pitfall 5), wrong canvas coordinates (Pitfall 6), canvas wiped on modal reopen (Pitfall 11), no dismiss path (Pitfall 10), drawing continues after pointer up (Pitfall 14)

### Phase 5: Polish

**Rationale:** With all mechanics working, visual refinement and the differentiating aesthetic details can be applied without risk of breaking functional interactions. Polish is last because aesthetic changes can be reverted without structural impact.
**Delivers:** String sag via SVG quadratic bezier, weathered card aesthetic (CSS sepia/filter), connect mode cursor and highlight feedback, card rotation variety, modal open/close transitions, cork texture refinement.
**Addresses:** String sag (differentiator), weathered aesthetic (differentiator), connect mode visual feedback (Pitfall 15)
**Avoids:** Premature aesthetic work that would be wiped by structural changes in earlier phases

### Phase Ordering Rationale

- Phase 1 before Phase 2: CSS must be correct for drag coordinate math to be accurate — visual validation first prevents debugging phantom layout issues during drag implementation
- Phase 2 before Phase 3: SVG connections require card positions that are managed by the drag system; the `pointermove` integration point cannot be written before drag exists
- Phase 2 before Phase 4: The click-vs-drag 5px threshold in the drag system is what triggers modal open; modal cannot be triggered correctly until this distinction exists
- Phase 3 before Phase 5: String sag polish requires the connection system to be functional
- Phase 5 last: All aesthetic refinements are non-destructive; doing them earlier risks being wiped by structural changes

### Research Flags

Phases with standard, well-documented patterns — skip `research-phase`:
- **Phase 1 (Static Layout):** Pure CSS; cork texture via CSS gradients is a solved problem
- **Phase 2 (Drag):** Pointer Events + `setPointerCapture` is fully specified in MDN; pitfalls are well-documented
- **Phase 3 (SVG Connections):** SVG coordinate system and `createElementNS` usage is fully specified
- **Phase 4 (Modal + Canvas):** `<dialog>` and Canvas 2D APIs are fully specified with `devicePixelRatio` patterns
- **Phase 5 (Polish):** CSS filters, bezier curves, and transitions are well-documented

No phases require additional research. All APIs are widely available, fully specified, and the pitfalls are known and documented in PITFALLS.md.

---

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | All APIs verified against MDN specification; all have "Widely available" baseline status |
| Features | MEDIUM-HIGH | Detective board trope is extremely consistent across 20+ well-known references; table stakes are unambiguous |
| Architecture | HIGH | Standard patterns for vanilla JS board interactions; no novel architecture required; build order derived from hard dependency chain |
| Pitfalls | HIGH | All pitfalls are specification-level behaviors (pointer capture, coordinate spaces, devicePixelRatio); not opinions or guesses |

**Overall confidence:** HIGH

### Gaps to Address

- **Cork texture specifics:** The CSS gradient recipe for cork simulation is not specified in research — will require iteration during Phase 1 to look right. Low risk; purely aesthetic.
- **Card content/theming:** FEATURES.md defers rich media entirely; the specific evidence content (what the 5 pre-set cards actually say and represent) is a design decision not addressed in research. Needs creative input, not technical research.
- **Click-vs-drag threshold:** The 5px movement threshold for distinguishing a click from a drag is a recommendation, not a measurement. May need tuning based on feel during Phase 2 implementation.
- **Canvas save format:** `toDataURL()` is specified as the persistence mechanism, but the format (PNG default vs JPEG) has memory implications if cards accumulate large drawings. Acceptable for a 5-card PoC.

---

## Sources

### Primary (HIGH confidence)
- MDN Pointer Events API — `pointerdown`, `pointermove`, `pointerup`, `setPointerCapture`, `releasePointerCapture`
- MDN HTMLDialogElement — `showModal()`, native Escape handling, top-layer, `::backdrop`
- MDN HTMLCanvasElement — `getContext('2d')`, `toDataURL()`, `devicePixelRatio` scaling pattern
- MDN SVG — `pointer-events` attribute, `createElementNS`, `<line>` element, coordinate system with `position: fixed`
- MDN getBoundingClientRect — viewport-relative coordinates, post-transform position
- MDN CSS cursor — `grab`, `grabbing`, `crosshair` baseline status

### Secondary (MEDIUM confidence)
- Training knowledge: True Detective S1, It's Always Sunny "Pepe Silvia", Homeland, A Beautiful Mind, Mindhunter — physical board table stakes
- Training knowledge: L.A. Noire, Return of the Obra Dinn, Disco Elysium, Sherlock Holmes: Crimes & Punishments, Pentiment — game investigation UI patterns
- Training knowledge: Milanote, Miro, Obsidian Canvas — digital tools emulating the physical board aesthetic

---
*Research completed: 2026-02-24*
*Ready for roadmap: yes*
