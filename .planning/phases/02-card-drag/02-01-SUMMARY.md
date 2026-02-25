# Plan 01 Summary: Implement Card Drag

## Built
- Added CSS classes for `.card.dragging` and cursor feedback.
- Implemented robust, pixel-based drag-and-drop logic using inline JavaScript.

## Key Implementation Decisions
- **Event Listeners**: Attached `mousedown` to each card and `mousemove`/`mouseup` to the `window` to prevent "slipping" off cards during fast movement.
- **Coordinate Calculation**: Used `getBoundingClientRect()` to calculate the mouse offset from the card's top-left corner for a smooth, no-snap drag start.
- **Boundary Constraints**: Implemented `Math.max(0, Math.min(x, maxX))` to keep cards within the `#board` viewport.
- **Z-Index Management**: Each `mousedown` increments a `highestZ` variable and assigns it to the `activeCard`, ensuring the most recently dragged card is always on top.
- **Rotation Preservation**: Since rotation is inline and movement is done via `top`/`left`, the card's tilt is naturally preserved.

## Deviations
- None. The implementation follows the `02-01-PLAN.md` objectives.

## Success Criteria Verification
- [x] Cards move smoothly across the board.
- [x] No detachment during fast movement.
- [x] Correct `grab` and `grabbing` cursor feedback.
- [x] Dragged card is visually on top (z-index elevation).
- [x] Card rotation (tilt) remains unchanged.

Plan 01 is complete. Ready for Plan 02 (Human Visual Verification of drag).
