# Phase 06 Research: Note System

## Implementation Strategy

### UI Integration (Expanded View)
- **Note Container**: Add a `<div>` with a `<textarea>` inside the `.dialog-content` in the `<dialog>`.
- **Styling**:
  - `textarea`: Use `font-family: 'Courier New'`, `background: transparent`, `border: 1px dashed #ccc`, and a physical padding.
  - **Dynamic Height**: Ensure the textarea is large enough for several lines of notes but doesn't overflow the dialog.

### State & Persistence Logic
- **`cardNotes` Object**: Create a global `cardNotes` object (similar to `cardData` for drawings) to store note text by `cardIndex`.
- **Auto-Save on Input**:
  - Attach an `input` event listener to the `<textarea>`.
  - On every keystroke, update `cardNotes[currentCardIndex]` with `textarea.value`.
  - Immediately save the updated `cardNotes` to `localStorage` as `investigation_notes`.

### Lifecycle Handling
- **`openCard(card)`**:
  - Fetch the note from `cardNotes[currentCardIndex]`.
  - Populate the `textarea.value` with the retrieved note or an empty string if none exists.
- **`initNotes()`**: On page load, retrieve `investigation_notes` from `localStorage` to populate the global `cardNotes` object.

### Technical Challenges
- **Event Jitter**: For a 5-card PoC, saving on every `input` event is low cost and provides a seamless "no-save-button" experience.
- **Scroll Management**: Ensure that typing long notes doesn't disrupt the modal's layout.

## Verification Strategy
- **Manual**:
  1. Open a card and type a detailed note in the new field.
  2. Close the modal and re-open the same card. Verify the note is still there.
  3. Open a different card. Verify its note field is empty (or has its own unique note).
  4. Refresh the browser. Verify the notes persist.
