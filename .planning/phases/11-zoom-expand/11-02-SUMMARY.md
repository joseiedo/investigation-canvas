# Phase 11-02: Animated Zoom & Direct Drawing

## Goal
Implement the core "Zoom Expand" behavior and allow drawing directly on the board cards.

## Plan
1.  **Animated Zoom Logic:**
    - Implement `animateZoom(targetX, targetY, targetZoom)` function using `requestAnimationFrame`.
    - Function should calculate target `panX` and `panY` to center the card on the left.
    - Save the original `panX`, `panY`, and `zoom` to return to them on un-zoom.
2.  **Refactor `openCard`:**
    - Replace the `<dialog>.showModal()` with a call to `animateZoom`.
    - Show the `#focus-backdrop` and slide in the `#desk-panel`.
    - Handle the "dimming" by fading in the backdrop.
3.  **Refactor Drawing Logic:**
    - Update `mousemove`, `mousedown`, `mouseup` to work on the active card's canvas.
    - Coordinate translation: Since we are drawing directly on the zoomed card, we need to handle the zoom factor and the high-res internal scale of the canvas.
4.  **Un-zoom Interaction:**
    - Add event listener to `#focus-backdrop` and the close button to trigger the reverse animation.
    - Hide the desk panel and backdrop.
5.  **Clean Up:**
    - Remove the old `expanded-canvas` logic and mirror function.
    - Ensure title and note updates still save to localStorage and update the card correctly.

## Success Criteria
- [ ] Clicking a card triggers a smooth animated zoom.
- [ ] The card is perfectly centered on the left side of the screen at a large scale.
- [ ] Drawing on the card is smooth and accurate at the zoomed-in level.
- [ ] Clicking the background zooms back out smoothly.
- [ ] All persistence (drawings, notes, titles) works as before.
