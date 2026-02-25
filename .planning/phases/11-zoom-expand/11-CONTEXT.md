# Phase 11 Context: Zoom Expand & Split View

## Phase Goal
Refactor the expansion mechanism to use an animated zoom into a split-screen "desk" view.

## Core Strategy
1.  **High-Res Cards:** Update the base resolution of evidence card canvases (800x500) and scale down via CSS.
2.  **Smooth Zoom Animation:** Implement a `requestAnimationFrame` interpolation for `panX`, `panY`, and `zoom`.
3.  **Physical Desk Panel:** Add a side-sliding UI component for tools and notes, styled like a physical surface.
4.  **Direct Drawing:** Shift the drawing event listeners from the modal dialog directly to the board's cards while they are zoomed in.
5.  **Un-zoom Interaction:** Close the desk view and return to the original board state by clicking the dimmed background.

## Dependencies
- Phase 10 (Contextual Analysis) completed.
- Existing `panX`, `panY`, `zoom` global variables.
- Existing `cardData` (drawings) and `cardNotes` (text) persistence.

## Success Criteria
- [ ] Clicking a card animates the zoom smoothly.
- [ ] Card is positioned left-center when zoomed.
- [ ] Desk panel slides in from the right.
- [ ] Rest of the board is dimmed.
- [ ] Drawing works directly on the card at high resolution.
- [ ] Clicking the dimmed area un-zooms the board.
