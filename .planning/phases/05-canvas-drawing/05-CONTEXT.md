# Phase 05 Context: Canvas Drawing & Board Sync

## Vision
To enhance the "investigation" feel, users should be able to circle faces, cross out names, or underline clues directly on the evidence. Using a red marker adds that messy, urgent "conspiracy wall" aesthetic. Most importantly, these marks shouldn't just exist in the modal; they should be visible on the board cards themselves, making the board feel "lived-in" as the investigation progresses.

## Essentials
- **Drawing Surface**: A transparent canvas overlaying the image/text in the expanded view.
- **Red Marker**: A semi-opaque, rough-edged stroke to simulate a marker pen.
- **Eraser**: A tool to remove strokes.
- **Board Sync**: Any drawing made in the modal must appear on the corresponding card on the main board, scaled correctly.
- **Non-Destructive**: Drawings must not overwrite the underlying evidence content.

## Boundaries
- **Single Color**: Only red marker for this phase.
- **Fixed Resolution**: The canvas resolution should match the expanded view display to keep coordinate mapping simple.
- **Local Persistence**: Full persistence is reserved for Phase 7, but drawings should persist during the current session (open/close modal).
