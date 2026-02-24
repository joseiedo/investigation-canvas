# Requirements: Investigation Canvas

**Defined:** 2026-02-24
**Core Value:** A tactile, movie-like investigation board where players can visually connect clues — the feel of a real detective's conspiracy wall.

## v1 Requirements

Requirements for the initial PoC release.

### Board

- [ ] **BOARD-01**: Board fills the full viewport (100vw × 100vh) with a cork board texture background and no visible scrollbars
- [ ] **BOARD-02**: 5 pre-set evidence cards (rectangles and/or squares) are displayed on the board with individual title labels, hard-coded in HTML

### Drag

- [ ] **DRAG-01**: User can drag any evidence card freely to any position on the board
- [ ] **DRAG-02**: Cursor shows `grab` when hovering an evidence card at rest, and `grabbing` while actively dragging
- [ ] **DRAG-03**: A card being dragged visually lifts above all other cards (z-index elevation while dragging)

### Connections

- [ ] **CONN-01**: User can toggle a "connect mode" via a visible button on the board
- [ ] **CONN-02**: In connect mode, user can click two separate evidence cards sequentially to draw a red string line connecting them
- [ ] **CONN-03**: Red string lines update their endpoints in real time as connected cards are dragged to new positions
- [ ] **CONN-04**: Red string lines display as curved (quadratic bezier sag) to simulate a string drooping under gravity

### Expanded View

- [ ] **EXPV-01**: Clicking (not dragging) an evidence card opens a fullscreen expanded view (native `<dialog>`) showing the card's title and details; Escape key and a close button both dismiss it

## v2 Requirements

Deferred to a future iteration.

### Cards & Aesthetics

- **CARD-01**: Each evidence card has a visible thumbtack/pin at the top (CSS pseudo-element)
- **CARD-02**: Each evidence card has a slight random rotation applied (lived-in, physical feel)

### Annotation

- **ANNO-01**: In the expanded view, user can draw freehand on the evidence card using a canvas element
- **ANNO-02**: In the expanded view, user can type a text note in a textarea field
- **ANNO-03**: Freehand drawings and text notes persist across open/close of the expanded view within the same browser session

## Out of Scope

Explicitly excluded from the PoC. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Dynamic add/remove of cards | Distracts from validating core feel; fix 5 cards for PoC |
| Persistent state (localStorage/backend) | Engineering concern, not a feel concern; ephemeral is fine |
| Board zoom and pan | Not needed when 5 cards fit in full viewport |
| Mobile/touch support | Desktop mouse-first; parallel interaction model adds scope |
| Multi-user / collaborative editing | Production concern entirely out of PoC scope |
| Undo/redo | Command-pattern architecture overkill for 5-card PoC |
| Deleting connections | Adds UI complexity; permanent connections fine for PoC |
| Rich media evidence (images, video) | Rectangular placeholders with labels are sufficient for PoC |
| Multiple string colors | One red color validates the feel |
| Non-rectangular card shapes | Rectangle/square is sufficient for PoC |

## Traceability

Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| BOARD-01 | — | Pending |
| BOARD-02 | — | Pending |
| DRAG-01 | — | Pending |
| DRAG-02 | — | Pending |
| DRAG-03 | — | Pending |
| CONN-01 | — | Pending |
| CONN-02 | — | Pending |
| CONN-03 | — | Pending |
| CONN-04 | — | Pending |
| EXPV-01 | — | Pending |

**Coverage:**
- v1 requirements: 10 total
- Mapped to phases: 0 (populated after roadmap creation)
- Unmapped: 10 ⚠️

---
*Requirements defined: 2026-02-24*
*Last updated: 2026-02-24 after initial definition*
