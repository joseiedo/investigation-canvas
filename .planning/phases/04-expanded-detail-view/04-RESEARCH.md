# Phase 04 Research: Expanded Detail View

## Implementation Strategy

### HTML `<dialog>` Element
- **Structure**: Add a `<dialog id="card-detail">` to the `<body>`. Include a `<h1>` for the title and a `<button>` to close.
- **Styling**:
  - `::backdrop`: Use a dark, semi-transparent blur (`backdrop-filter: blur(4px)`) to emphasize the immersive "look closer" feel.
  - **Modal Styling**: Monospace fonts, cream background (matching cards), and typewriter aesthetics.
- **Methods**: Use `.showModal()` and `.close()`.

### Click vs. Drag Detection
- **State Variables**: `isDragging`, `startX`, `startY`.
- **Logic**:
  1. On `mousedown`: Record `startX`, `startY`. Set `isDragging = false`.
  2. On `mousemove`: Calculate distance from start. If `distance > 5px`, set `isDragging = true`.
  3. On `mouseup`: Only if `!isDragging` and `!isConnectMode`, trigger the "open expanded view" logic.

### Dynamic Content
- **Function `openCard(card)`**:
  - Extract title from the card's `.card-title` element.
  - Update the `<dialog>` content.
  - Call `dialog.showModal()`.

### Handling Escape and Close
- Native `<dialog>` handles `Escape` automatically.
- Add an event listener to the close button to call `dialog.close()`.

## Verification Strategy
- **Automated**: Use a script to simulate a click (mousedown/mouseup without movement) and check if the `<dialog>` has the `open` attribute.
- **Manual**:
  1. Verify dragging a card does *not* open the modal.
  2. Verify clicking a card *does* open the modal.
  3. Verify clicking a card in "Connect Mode" does *not* open the modal.
  4. Verify Escape key closes the modal.
