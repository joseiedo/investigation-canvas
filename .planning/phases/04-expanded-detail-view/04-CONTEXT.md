# Phase 04 Context: Expanded Detail View

## Vision
To complete the "detective wall" experience, users need to be able to "look closer" at any piece of evidence. Clicking a card should open a focused, immersive view that displays the evidence more clearly. This must be distinct from the drag interaction to prevent accidental opens while reorganizing the board.

## Essentials
- **Distinguish Click vs. Drag**: Use a movement threshold (e.g., 5px) to ensure only intentional clicks open the expanded view.
- **Native Immersive UI**: Use the HTML `<dialog>` element for robust modal behavior (focus trapping, backdrop, Escape key handling).
- **Clear Information**: Display the card's title in the expanded view.
- **Easy Dismissal**: Close button and Escape key must work reliably.

## Boundaries
- **Minimal Content**: For this PoC phase, only the title needs to be shown (matching EXPV-01).
- **Single File**: Maintain the project's single-file structure.
- **Non-blocking**: Opening the expanded view should not reset the board's state or connections.
- **No Connect Mode Interaction**: Clicking cards while in "Connect Mode" should *not* open the expanded view.
