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

## Phase Details

### Phase 5: Canvas Drawing & Board Sync
**Goal**: Allow users to "mark up" evidence in the expanded view and see those marks reflected on the board
**Depends on**: Phase 4
**Requirements**: ANNO-01, ANNO-02
**Success Criteria**:
  1. A drawing mode exists in the expanded view with a "Red Marker" and an "Eraser" tool
  2. Drawing on the expanded evidence is smooth and responsive
  3. Closing the modal shows the same drawing (scaled down) on the card on the board
**Plans**: TBD

### Phase 6: Note System
**Goal**: Provide a typewriter-style note field for recording theories
**Depends on**: Phase 4
**Requirements**: ANNO-03
**Success Criteria**:
  1. A textarea exists in the expanded view for each card
  2. Typing in the note field saves the text immediately
  3. Re-opening a card restores its previous note
**Plans**: TBD

### Phase 7: Full Persistence
**Goal**: Ensure the investigation board survives browser refreshes
**Depends on**: Phase 1, 2, 3, 5, 6
**Requirements**: PERS-01, PERS-02, PERS-03, PERS-04
**Success Criteria**:
  1. Refreshing the browser restores all card positions exactly as they were
  2. All SVG connections are redrawn on load
  3. All drawings and notes are restored
  4. A "Reset Board" button clears all state and resets to the default 5-card layout
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5 → 6 → 7

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Static Board Layout | 2/2 | Completed | 2026-02-24 |
| 2. Card Drag | 2/2 | Completed | 2026-02-24 |
| 3. SVG Connections | 2/2 | Completed | 2026-02-24 |
| 4. Expanded Detail View | 2/2 | Completed | 2026-02-24 |
| 5. Canvas Drawing & Board Sync | 2/2 | Completed | 2026-02-24 |
| 6. Note System | 2/2 | Completed | 2026-02-24 |
| 7. Full Persistence | 2/2 | Completed | 2026-02-24 |
