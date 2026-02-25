# Phase 07 Research: Full Persistence

## Implementation Strategy

### Position Persistence
- **State**: A `cardPositions` object `{ index: { top, left } }`.
- **Saving**: Update `cardPositions[activeCard.dataset.index]` inside the `onMouseUp` handler.
- **Restoring**: On page load, iterate through cards and apply saved `top`/`left` styles if they exist.

### Connection Persistence
- **State**: The existing `connections` array needs to be serialized.
- **Serialization**: Store as an array of pairs: `[{ fromIndex: 0, toIndex: 1 }, ...]`.
- **Saving**: Update `localStorage` whenever a new connection is created.
- **Restoring**: On page load, iterate through the saved pairs and call `createConnection(card[from], card[to])`.

### Reset Board Logic
- **Action**: A button that calls `localStorage.clear()` (or specifically removes our keys) and then `window.location.reload()`.
- **UI**: A small, discreet "Reset Board" button in a corner (e.g., bottom-right).

### Storage Consolidation
- Centralize all saving into a `saveAll()` function or maintain separate keys for positions, connections, drawings, and notes. Separate keys are easier to debug.
  - `investigation_drawings` (Done)
  - `investigation_notes` (Done)
  - `investigation_positions` (New)
  - `investigation_connections` (New)

## Technical Challenges
- **SVG Layer Timing**: Connections must be restored *after* card positions are applied, otherwise the endpoints will be wrong until the card is moved.
- **Center Calculation**: Ensure `updatePath` is called immediately after restoring connections so the red strings appear in the correct place on load.

## Verification Strategy
- **Manual**:
  1. Move all cards to new positions.
  2. Draw several red strings.
  3. Refresh the page. Verify everything is exactly where you left it.
  4. Click "Reset Board". Verify the board returns to its original layout and strings disappear.
