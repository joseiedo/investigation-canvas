# Phase 08 Context: Board Navigation (Pan/Zoom)

## Vision
As the investigation grows, a single viewport is no longer enough. Users need a vast cork board that they can explore. Pan and Zoom provide this freedom, allowing the user to "step back" to see the big picture (all connections) or "lean in" to focus on specific clues. The interaction must remain tactile—panning should feel like grabbing the board and sliding it, and zooming should feel smooth and centered on the cursor.

## Essentials
- **Pan**: Dragging the board background (not a card) moves the entire workspace.
- **Zoom**: Mouse wheel scrolling scales the board up and down.
- **Coordinate Integrity**: Dragging cards and drawing red strings must work correctly regardless of the current zoom level and pan offset.
- **Visual Continuity**: The cork texture and vignette should ideally stay pinned to the viewport or tile seamlessly to maintain the "infinite board" feel.

## Boundaries
- **Infinite vs. Large**: For this PoC, we will implement a "large" board (e.g., 5000x5000px) or an infinite-feeling transform-based system.
- **No Mini-map**: Minimap navigation is out of scope for this phase.
- **Mouse Only**: Zooming is wheel-based; panning is middle-mouse or space+drag (or simple background drag).
