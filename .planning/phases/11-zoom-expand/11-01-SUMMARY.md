# Phase 11-01: High-Res Canvas & Desk UI

## Goal
Prepare the project for a zoom-in expansion by increasing card canvas resolution and adding the side desk panel.

## Plan
1.  **Refactor Card Canvas:**
    - Increase the `card-canvas` base resolution (e.g., 800x500).
    - Use CSS to scale it back down to fit the physical card size (160x100).
    - Update `init()` to handle the new resolution and drawing scaling.
2.  **Desk Side Panel:**
    - Create a `#desk-panel` `div` that is fixed on the right (width: 40%).
    - Move all the UI elements from the existing `<dialog>` into the `#desk-panel`.
    - Style the `#desk-panel` to look like a physical desk (wood or cork texture, shadows).
3.  **Backdrop Overlay:**
    - Create a `#focus-backdrop` `div` that is absolute in `#board` (z-index: 500) and hidden by default.
    - Set its pointer-events such that it can catch clicks to "un-zoom".
4.  **UI Elements Update:**
    - Add a "Close" button to the `#desk-panel`.
    - Style the toolbar and notes to fit the side panel layout.

## Success Criteria
- [ ] Card canvases are 800x500 pixels internally but 160x100 pixels visually.
- [ ] Existing drawings are correctly scaled and rendered on the new high-res canvases.
- [ ] A hidden desk panel and focus backdrop are added to the HTML/CSS.
- [ ] The old `<dialog>` is removed or its content is migrated to the new panel.
