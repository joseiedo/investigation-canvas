# Phase 10 Research: Contextual Analysis

## Implementation Strategy

### Multi-color Markers (Images)
- **UI Update**: Add color swatches (Red, Blue, Black) to the `drawing-toolbar`.
- **Logic**:
  - `expCtx.strokeStyle` updates based on the selected swatch.
  - Colors: Red (`rgba(231, 76, 60, 0.7)`), Blue (`rgba(52, 152, 219, 0.7)`), Black (`rgba(44, 62, 80, 0.7)`).
- **Persistence**: `toDataURL()` already captures the colors, so no changes needed to the storage logic.

### Text Highlighting (Documents)
- **Detection**: Check if `card.querySelector('img')` exists in `openCard`.
- **UI adaptation**: If no image, hide the `drawing-toolbar` and show a `highlight-toolbar` (or just enable the behavior).
- **Highlighting Logic**:
  - **Option A**: Use `window.getSelection()` and `document.execCommand('backColor')` (Deprecated but simple).
  - **Option B**: Use a library (Out of scope).
  - **Option C**: Manual implementation. Since we want persistence, we need to store the indices of highlighted text.
  - **Surgical Choice**: For a PoC, we will wrap the text in a `<span>` and allow the user to "paint" highlights using a mouseover effect while holding a button, OR simply allow standard selection and store the result.
  - **Decision**: To keep it tactile and persistent, we will store the HTML content of the `.card-text` (including `<span>` tags for highlights) in `localStorage`.

### Mirroring Highlights
- When the modal's text content changes (via highlighting), update the board card's `.card-text` HTML immediately.
- Style the `span.highlight` with `background-color: yellow;`.

## Technical Challenges
- **HTML Persistence**: Saving raw HTML to `localStorage` can be risky if not handled carefully, but for internal PoC data, it is the most direct way to persist complex formatting like highlights.
- **Dueling Contexts**: Ensuring the canvas is hidden/disabled when the text highlighter is active to prevent intercepting clicks.

## Verification Strategy
- **Manual**:
  1. Open an image card. Switch between Red, Blue, and Black. Verify the colors work and mirror to the board.
  2. Open a text card (Alibi). Verify the drawing toolbar is hidden or disabled.
  3. Select/Highlight text. Verify it turns yellow and persists after a refresh.
  4. Verify the highlight appears on the miniature board card.
