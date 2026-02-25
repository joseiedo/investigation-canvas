# Phase 02 Context: Card Drag

## Vision
The investigation board should feel interactive and tactile. Users should be able to pick up any evidence card and move it to a new location on the cork board. The interaction must be smooth and robust, ensuring the card never "slips" from the cursor even during fast movements.

## Essentials
- **Smoothness**: Use `requestAnimationFrame` if needed, but standard mouse events should suffice for 5 elements.
- **Physicality**: Cursor feedback (`grab`/`grabbing`) and visual elevation (`z-index` and enhanced shadow).
- **Z-Index Management**: The card being dragged must always be on top.
- **Coordinate Integrity**: Calculate the offset between the mouse click and the card's top-left corner so the card doesn't "snap" its center to the cursor on start.

## Boundaries
- **No Libraries**: Pure Vanilla JavaScript.
- **Desktop Only**: Mouse-based interactions; touch support is out of scope.
- **No Persistence**: Card positions do not need to persist across page reloads (for now).
- **Rotation**: The natural tilt (rotation) of the cards must be preserved during and after the drag.
