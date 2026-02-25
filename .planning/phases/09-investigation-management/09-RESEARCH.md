# Phase 09 Research: Investigation Management

## Implementation Strategy

### Card Renaming
- **UI Integration**: In the expanded view, make the `#dialog-title` editable.
  - Option A: Change `<h1>` to an `<input type="text">`.
  - Option B: Use `contenteditable="true"`.
  - **Decision**: Use an `<input>` for better control and accessibility.
- **State & Persistence**:
  - Global `cardTitles` object indexed by `data-index`.
  - Update `cardTitles` on `input` or `change` event.
  - Update the `.card-title` on the main board in real-time.
  - Add to `saveToStorage()`.

### Connection Deletion
- **Trigger**: User needs a way to target a specific "red string".
- **Mechanism**: 
  - Since the SVG layer has `pointer-events: none`, the individual `<path>` elements cannot be clicked directly unless we change this.
  - Change `connections-layer` to `pointer-events: none` but set individual `<path>` elements to `pointer-events: stroke`.
  - **Action**: Add a `contextmenu` (right-click) listener to each `<path>`.
- **Logic**:
  - On right-click: `path.remove()`, remove from the `connections` array, and `saveToStorage()`.
  - **UX**: Prevent the default browser context menu when right-clicking a string.

### Technical Challenges
- **Path Selection**: Ensuring the user can easily "hit" the 3px line with a right-click. We can add a wider transparent "hit area" path if needed, but `pointer-events: stroke` on a 3px line is usually sufficient.
- **Syncing Title UI**: Ensuring the board's card title updates immediately when the modal input changes.

## Verification Strategy
- **Manual**:
  1. Open a card, change "Suspect A" to "Arthur Miller". Verify the card on the board updates.
  2. Refresh the page; verify the new name persists.
  3. Right-click a red string. Verify it disappears.
  4. Refresh the page; verify the string stays gone.
