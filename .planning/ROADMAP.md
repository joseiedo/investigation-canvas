# Roadmap: Investigation Canvas

## Overview

Build a tactile detective conspiracy board PoC in four sequential phases. Each phase delivers one complete, testable capability. The dependency chain is hard: visual layout must be correct before drag coordinates can be trusted, drag must work before SVG connections can track card positions, and the click-vs-drag distinction from the drag phase is the trigger for the expanded modal. Polish is baked into each phase rather than deferred — this is a feel-first PoC.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Static Board Layout** - Cork board background and 5 hard-coded evidence cards styled with pin aesthetic
- [ ] **Phase 2: Card Drag** - Freely draggable cards with correct cursor feedback and z-index elevation
- [ ] **Phase 3: SVG Connections** - Connect mode toggle and red string lines that track dragged cards in real time
- [ ] **Phase 4: Expanded Detail View** - Card click opens a native dialog with title display, dismiss via Escape or close button

## Phase Details

### Phase 1: Static Board Layout
**Goal**: The board exists as a correctly styled, full-viewport detective wall with 5 evidence cards visible and positioned
**Depends on**: Nothing (first phase)
**Requirements**: BOARD-01, BOARD-02
**Success Criteria** (what must be TRUE):
  1. Opening the file in a browser shows a full-viewport board with no scrollbars — the board fills the entire window
  2. The board has a cork/dark textured background that reads as a detective wall, not a generic webpage
  3. 5 distinct evidence cards are visible on the board, each with its own title label
  4. Cards have a pinned, physical appearance (pin pseudo-element visible at top of each card)
**Plans**: 2 plans

Plans:
- [ ] 01-01-PLAN.md — Create index.html: full-viewport cork board + 5 evidence cards (HTML/CSS)
- [ ] 01-02-PLAN.md — Human visual verification of board aesthetic

### Phase 2: Card Drag
**Goal**: Users can freely reposition evidence cards anywhere on the board with correct visual and cursor feedback
**Depends on**: Phase 1
**Requirements**: DRAG-01, DRAG-02, DRAG-03
**Success Criteria** (what must be TRUE):
  1. User can click and drag any card to any position on the board and release it there
  2. A card held during fast mouse movement does not detach or freeze — the drag always follows the cursor
  3. Cursor shows `grab` when hovering a card at rest and switches to `grabbing` while the card is being dragged
  4. A card being dragged visually sits above all other cards — no other card appears on top of the dragged one
**Plans**: TBD

### Phase 3: SVG Connections
**Goal**: Users can draw red string connections between evidence cards and those lines persist and update as cards are repositioned
**Depends on**: Phase 2
**Requirements**: CONN-01, CONN-02, CONN-03, CONN-04
**Success Criteria** (what must be TRUE):
  1. A visible "connect mode" button exists on the board; clicking it toggles connect mode on and off
  2. In connect mode, clicking card A then card B draws a visible red line between them
  3. When a connected card is dragged to a new position, its red string line endpoint moves with it in real time
  4. Red string lines visually sag downward (curved, not straight) to simulate a physical string under gravity
**Plans**: TBD

### Phase 4: Expanded Detail View
**Goal**: Clicking an evidence card opens a fullscreen detail view of that card, dismissible via Escape or a close button
**Depends on**: Phase 2
**Requirements**: EXPV-01
**Success Criteria** (what must be TRUE):
  1. Clicking (not dragging) a card opens a fullscreen dialog showing that card's title
  2. Pressing Escape while the dialog is open closes it and returns to the board
  3. A visible close button inside the dialog closes it when clicked
  4. After the dialog closes, all cards and connections are in the same state as before it was opened
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Static Board Layout | 0/2 | Not started | - |
| 2. Card Drag | 0/TBD | Not started | - |
| 3. SVG Connections | 0/TBD | Not started | - |
| 4. Expanded Detail View | 0/TBD | Not started | - |
