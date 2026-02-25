# Phase 05 Research: Canvas Drawing & Board Sync

## Implementation Strategy

### Canvas Layering (Expanded View)
- **Overlay Canvas**: A `<canvas>` element with `pointer-events: auto` that sits on top of the `.dialog-content` (above images and text).
- **Coordinate Mapping**: Ensure the canvas matches the `dialog-content` dimensions exactly to prevent offset issues during drawing.

### Drawing Logic (Red Marker & Eraser)
- **State Variables**: `isDrawing`, `currentTool` (pen/eraser).
- **Pen Style**: `ctx.strokeStyle = '#e74c3c'`, `ctx.lineWidth = 4`, `ctx.lineJoin = 'round'`, `ctx.lineCap = 'round'`.
- **Eraser Style**: Use `ctx.globalCompositeOperation = 'destination-out'` to clear parts of the canvas.

### Real-time Board Mirroring
- **Dual Canvases**: Each `.card` on the board needs its own miniature `<canvas>`.
- **Syncing**:
  - When drawing in the expanded view, immediately draw the same stroke on the miniature card's canvas.
  - Scale the coordinates: `miniX = expandedX * (miniWidth / expandedWidth)`.
- **Alternative (Capture/Redraw)**:
  - After closing the modal (or during drawing), capture the expanded canvas and draw it into the card's canvas using `drawImage(expandedCanvas, 0, 0, miniWidth, miniHeight)`. This is simpler than duplicating every stroke in real-time.

### Event Handling
- **`mousedown` / `mousemove` / `mouseup`**: Attach to the expanded canvas.
- **Tool Switcher**: A simple UI in the dialog (e.g., icons or text buttons for "Marker" and "Eraser").

### Technical Challenges
- **Canvas Resizing**: If the modal size changes, the canvas content might stretch or clear. We need a fixed-size drawing area within the modal.
- **Performance**: Redrawing both canvases in real-time shouldn't be an issue for 5 cards, but we'll monitor for lag.

## Verification Strategy
- **Manual**:
  1. Open a card, select "Marker", and circle a face.
  2. Switch to "Eraser" and remove part of the circle.
  3. Close the modal and verify the miniature version appears on the board card.
  4. Re-open the modal and verify the drawing is still there.
