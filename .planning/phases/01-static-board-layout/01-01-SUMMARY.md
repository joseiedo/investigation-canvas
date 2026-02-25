# Plan 01 Summary: Create static board layout

## Built
- Created `index.html` at project root.
- Implemented a single-file investigation canvas using HTML and CSS.

## Key Implementation Decisions
- **Background:** Used `#2a1f14` dark brown base with an inline SVG `feTurbulence` (type `fractalNoise`, baseFrequency `0.75`) for an organic cork texture.
- **Vignette:** Added a radial-gradient overlay with `pointer-events: none` to create a cinematic atmosphere without interfering with future interactions.
- **Evidence Cards:** 
  - 5 cards (Suspect A, Victim, Crime Scene, Alibi, Witness) with cream backgrounds (`#fdf6e3`).
  - Layered `box-shadow` for depth.
  - Typewriter font (`Courier New`) for titles.
  - Individual `rotate` and `percentage-based` positioning for a natural, hand-pinned look.
- **Thumbtack Pins:** Created via `.card::before` pseudo-elements with a red radial gradient and shadow to simulate 3D pins.

## Deviations
- None. The implementation strictly follows the requirements in `01-01-PLAN.md`.

## Success Criteria Verification
- [x] Full-viewport board, no scrollbars.
- [x] Dark cork texture with vignette.
- [x] 5 visible cards with typewriter titles.
- [x] Cream background, red pins, and strong shadows on cards.
- [x] Individual tilts for a hand-pinned aesthetic.

Phase 1, Plan 01 is complete. Ready for Plan 02 (Visual Verification).
