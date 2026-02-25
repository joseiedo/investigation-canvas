# Phase 08 Research: Board Navigation (Pan/Zoom)

## Implementation Strategy

### Transform Container
- **Structure**: Introduce `<div id="world">` inside `#board`.
- **Content**: Move all `.card` elements and the `#connections-layer` SVG inside `#world`.
- **CSS**: 
  - `#board`: `overflow: hidden; cursor: crosshair;` (when panning).
  - `#world`: `transform-origin: 0 0; transition: transform 0.05s ease-out;` (for smooth zoom).

### Pan Logic
- **Trigger**: `mousedown` on `#board` (if target is not a card).
- **State**: `panX`, `panY`.
- **Calculation**: 
  - On move: `panX += e.movementX`, `panY += e.movementY`.
  - Update: `world.style.transform = translate(${panX}px, ${panY}px) scale(${zoom})`.

### Zoom Logic
- **Trigger**: `wheel` event on `#board`.
- **Calculation**:
  - `zoom += e.deltaY * -0.001`.
  - Clamp zoom: `Math.max(0.2, Math.min(3, zoom))`.
  - **Focal Point**: To zoom into the cursor, we need to adjust `panX/Y` based on the mouse position relative to the scale change.
    ```javascript
    const mouseX = e.clientX - boardRect.left;
    const mouseY = e.clientY - boardRect.top;
    const worldX = (mouseX - panX) / zoom;
    const worldY = (mouseY - panY) / zoom;
    
    zoom = newZoom;
    panX = mouseX - worldX * zoom;
    panY = mouseY - worldY * zoom;
    ```

### Coordinate Adjustments
- **Card Dragging**: When dragging a card at `zoom != 1`, the movement delta must be scaled.
  - `cardX += e.movementX / zoom`.
- **SVG Connections**: Since the SVG layer is inside `#world`, `getBoundingClientRect()` will return scaled coordinates. We must either:
  1. Use card `offsetTop/Left` directly (which are in "world" units).
  2. Map `getBoundingClientRect()` back to world coordinates by dividing by zoom and subtracting pan.
  - **Decision**: Use `card.style.left/top` or `card.offsetLeft/Top` for `updatePath` to keep it scale-independent.

### Visual Consistency
- **Background**: Keep the cork background on `#board` (viewport-fixed) but maybe add a secondary "grain" layer inside `#world` that scales, or keep it fixed so the texture doesn't get blurry when zooming in.
- **Decision**: Keep cork texture fixed on `#board` for performance and crispness.

## Verification Strategy
- **Manual**:
  1. Drag the background; verify all cards and strings move together.
  2. Use scroll wheel; verify the board zooms in/out centered on the cursor.
  3. Drag a card while zoomed out (0.5x); verify it stays under the mouse.
  4. Draw a connection while zoomed in (2x); verify it tracks correctly.
