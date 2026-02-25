# Phase 02 Research: Card Drag

## Implementation Strategy

### Mouse Event Coordination
- **`mousedown` on `.card`**: 
  1. Record the current mouse coordinates (`clientX`, `clientY`).
  2. Calculate the offset between the mouse and the card's top-left corner (`offsetLeft`, `offsetTop`).
  3. Start the drag state.
  4. Bring the card to the top (`z-index`).
  5. Add a `.dragging` class for visual feedback (e.g., cursor: `grabbing`, enhanced shadow).
  6. Attach `mousemove` and `mouseup` to the `window` or `document` to handle fast mouse movements that might leave the card's bounds.

- **`mousemove` on `window`**:
  1. Calculate new `left` and `top` based on mouse position and the recorded offset.
  2. Update the card's style in pixels (`px`).
  3. Ensure the card stays within the `#board` boundaries (optional, but good for UX).

- **`mouseup` on `window`**:
  1. Remove the `mousemove` and `mouseup` listeners.
  2. End the drag state.
  3. Remove the `.dragging` class.
  4. Maintain the final `z-index` or reset it (Requirement DRAG-03 says "lifts above... WHILE dragging", so maybe reset it slightly, but it should still stay on top of other non-dragged cards).

### CSS Classes & Cursor Feedback
- **`.card`**: Add `cursor: grab;` by default.
- **`.card.dragging`**: Add `cursor: grabbing;` and a larger/deeper `box-shadow` to simulate physical elevation.

### Boundary Constraints
Since the board is `100vw` by `100vh`, the cards should ideally stay within these bounds.
- Board width: `document.body.clientWidth` or `#board.offsetWidth`.
- Board height: `document.body.clientHeight` or `#board.offsetHeight`.

### Technical Considerations
- **`z-index` Management**: A simple way is to use a `highestZ` variable and increment it on每drag start, or set a fixed high value (e.g., `1000`) for the dragging card and reset other cards to a lower value.
- **Preserving Rotation**: Since rotation is currently inline (e.g., `transform: rotate(-3deg)`), and we'll be moving the card via `top` and `left`, the rotation will naturally persist without extra code.

## Verification Strategy
- **Automated**: Use a script to simulate mouse events (`mousedown`, `mousemove`, `mouseup`) and check if the `style.top` and `style.left` of the card have updated correctly.
- **Manual**: Open `index.html` and verify the "feel" — no slipping, correct cursor, and smooth movement.
