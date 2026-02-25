# Phase 10 Context: Contextual Analysis

## Vision
The investigation is becoming more complex. A single red marker is no longer enough to categorize different types of clues on photos (e.g., Red for suspects, Blue for physical evidence, Black for dead ends). Simultaneously, text-based evidence requires a different analytical toolset: highlighting specific words or phrases rather than drawing over them. This phase introduces contextual tools—markers for images and highlighters for text—to support deeper, more nuanced investigation.

## Essentials
- **Multi-color Toolset**: Support for Red, Blue, and Black markers when analyzing images.
- **Text Highlighting**: Disable the drawing canvas for text cards and enable a persistent yellow highlighter for text selections.
- **Contextual UI**: The toolbar should adapt based on whether the evidence is an image or text.
- **Persistence & Sync**: Highlights and multi-color drawings must be saved to `localStorage` and mirrored to the board.

## Boundaries
- **Fixed Highlighter Color**: Yellow only for text highlighting in this phase.
- **No Overlap**: Canvas drawing is completely disabled for text cards to avoid UI clutter.
- **Standard Selection**: Use the browser's native text selection as the basis for highlighting if possible, or a custom overlay.
