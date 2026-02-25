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
- [ ] **Phase 5: Canvas Drawing & Board Sync** - Red marker and eraser drawing in modal with real-time scaling to main board
- [ ] **Phase 6: Note System** - Auto-saving text notes in expanded view
- [ ] **Phase 7: Full Persistence** - LocalStorage integration for positions, connections, drawings, and notes
- [ ] **Phase 8: Board Navigation (Pan/Zoom)** - Navigate a board larger than the viewport
- [ ] **Phase 9: Investigation Management** - Rename cards and delete red strings
- [ ] **Phase 10: Contextual Analysis (Highlighting)** - Multi-color markers for images; text highlighting for documents

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
**Plans**: 2 plans

### Phase 3: SVG Connections
**Goal**: Users can draw red string connections between evidence cards and those lines persist and update as cards are repositioned
**Depends on**: Phase 2
**Requirements**: CONN-01, CONN-02, CONN-03, CONN-04
**Success Criteria** (what must be TRUE):
  1. A visible "connect mode" button exists on the board; clicking it toggles connect mode on and off
  2. In connect mode, clicking card A then card B draws a visible red line between them
  3. When a connected card is dragged to a new position, its red string line endpoint moves with it in real time
  4. Red string lines visually sag downward (curved, not straight) to simulate a physical string under gravity
**Plans**: 2 plans

### Phase 4: Expanded Detail View
**Goal**: Clicking an evidence card opens a fullscreen detail view of that card, dismissible via Escape or a close button
**Depends on**: Phase 2
**Requirements**: EXPV-01
**Success Criteria** (what must be TRUE):
  1. Clicking (not dragging) a card opens a fullscreen dialog showing that card's title
  2. Pressing Escape while the dialog is open closes it and returns to the board
  3. A visible close button inside the dialog closes it when clicked
  4. After the dialog closes, all cards and connections are in the same state as before it was opened
**Plans**: 2 plans

### Phase 5: Canvas Drawing & Board Sync
**Goal**: Allow users to "mark up" evidence in the expanded view and see those marks reflected on the board
**Depends on**: Phase 4
**Requirements**: ANNO-01, ANNO-02
**Success Criteria**:
  1. A drawing mode exists in the expanded view with a "Red Marker" and an "Eraser" tool
  2. Drawing on the expanded evidence is smooth and responsive
  3. Closing the modal shows the same drawing (scaled down) on the card on the board
**Plans**: 2 plans

### Phase 6: Note System
**Goal**: Provide a typewriter-style note field for recording theories
**Depends on**: Phase 4
**Requirements**: ANNO-03
**Success Criteria**:
  1. A textarea exists in the expanded view for each card
  2. Typing in the note field saves the text immediately
  3. Re-opening a card restores its previous note
**Plans**: 2 plans

### Phase 7: Full Persistence
**Goal**: Ensure the investigation board survives browser refreshes
**Depends on**: Phase 1, 2, 3, 5, 6
**Requirements**: PERS-01, PERS-02, PERS-03, PERS-04
**Success Criteria**:
  1. Refreshing the browser restores all card positions exactly as they were
  2. All SVG connections are redrawn on load
  3. All drawings and notes are restored
  4. A "Reset Board" button clears all state and resets to the default 5-card layout
**Plans**: 2 plans

### Phase 8: Board Navigation (Pan/Zoom)
**Goal**: Allow the investigation to scale beyond a single viewport
**Depends on**: Phase 7
**Requirements**: BOARD-03
**Success Criteria**:
  1. User can drag the background to "pan" the board
  2. User can use the mouse wheel to "zoom" in and out
  3. Dragging cards and red strings works correctly at any zoom level
**Plans**: TBD

### Phase 9: Investigation Management
**Goal**: Add essential management tools for renaming and cleaning up connections
**Depends on**: Phase 7
**Requirements**: CARD-03, CONN-05
**Success Criteria**:
  1. Card titles can be edited in the expanded view and persist
  2. Red strings can be deleted (e.g., via right-click or a small 'x' on the line)
**Plans**: TBD

### Phase 10: Contextual Analysis (Highlighting)
**Goal**: Distinguish between visual markup for photos and text analysis for documents
**Depends on**: Phase 5, Phase 6
**Requirements**: ANNO-04, ANNO-05, ANNO-06
**Success Criteria**:
  1. Drawing toolbar supports Red, Blue, and Black markers for images
  2. Text-based cards (like Alibi) disable the drawing canvas
  3. Selecting text in a text card creates a persistent yellow highlight
  4. Highlights are mirrored to the board card view
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Static Board Layout | 2/2 | Completed | 2026-02-24 |
| 2. Card Drag | 2/2 | Completed | 2026-02-24 |
| 3. SVG Connections | 2/2 | Completed | 2026-02-24 |
| 4. Expanded Detail View | 2/2 | Completed | 2026-02-24 |
| 5. Canvas Drawing & Board Sync | 2/2 | Completed | 2026-02-24 |
| 6. Note System | 2/2 | Completed | 2026-02-24 |
| 7. Full Persistence | 2/2 | Completed | 2026-02-24 |
| 8. Board Navigation (Pan/Zoom) | 1/1 | Completed | 2026-02-24 |
| 9. Investigation Management | 0/TBD | Not started | - |
| 10. Contextual Analysis (Highlighting) | 0/TBD | Not started | - |
