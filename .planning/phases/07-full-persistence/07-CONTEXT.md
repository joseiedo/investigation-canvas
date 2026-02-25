# Phase 07 Context: Full Persistence

## Vision
An investigation board is only useful if it doesn't disappear when you close the tab. Full Persistence ensures that every decision — where a card is placed and how it's connected — is remembered. This transforms the PoC from a transient demo into a persistent tool. A "Reset Board" capability is also essential to allow for a fresh start.

## Essentials
- **Position Persistence**: Save and restore the `top`/`left` coordinates of all 5 evidence cards.
- **Connection Persistence**: Save and restore the list of "red string" connections.
- **Reset Board**: A clear UI action to wipe `localStorage` and return the board to its initial hard-coded state.
- **Background Saving**: Position and connection changes should save automatically without user intervention.

## Boundaries
- **Local Only**: All data remains in the user's browser `localStorage`. No cloud sync or accounts.
- **ID Stability**: Since we have 5 fixed cards, we rely on their `data-index` or hard-coded order for mapping saved data.
- **No Undo for Reset**: Once the board is reset, the previous state is permanently deleted from `localStorage`.
