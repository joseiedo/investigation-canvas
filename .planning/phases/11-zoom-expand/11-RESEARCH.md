# Phase 11 Research: Zoom Expand & Split View

## Zoom Logic
To zoom to a card and position it on the left (at 25% screen width):
- Target Screen X: `window.innerWidth * 0.25`
- Target Screen Y: `window.innerHeight * 0.5`
- Card World X, Y: `card.offsetLeft + card.offsetWidth / 2`, `card.offsetTop + card.offsetHeight / 2`
- Target Zoom: Need to scale the card up significantly (e.g., 3x or 4x) but bounded by screen height.
- Equations:
  - `target_panX = target_screenX - card_worldX * target_zoom`
  - `target_panY = target_screenY - card_worldY * target_zoom`

## Animation
- Use `requestAnimationFrame` for a smooth transition of `panX`, `panY`, and `zoom`.
- Or use CSS transitions on `#world`, but that might conflict with the manual pan/zoom logic.
- Manual interpolation is safer to keep `save()` logic consistent.

## High-Resolution Canvas
- Current `card-canvas` is 160x100.
- When zoomed 4x, it's 640x400 on screen.
- We should initialize `card-canvas` with higher resolution (e.g. 800x500) and scale it down with CSS.
- `ctx.scale(ratio, ratio)` can be used to keep drawing logic simple.

## Desk Panel
- A fixed overlay that slides in.
- Should have a "physical" feel (paper texture, shadows).
- Needs to handle card title editing and notes.

## Backdrop
- A `div` inside `#world` or `#board`?
- If inside `#world`, it must have a high `z-index` but lower than the active card.
- If outside `#world`, it's simpler but won't "pan" with the board.
- Best approach: A `div` in `#board` that is normally hidden, but when active, it sits between the world and the active card. This is tricky because the card is *inside* the world.
- Alternative: When zooming, temporarily move the card to a higher z-index container? No, that breaks connections.
- Better: The backdrop is in `#board`, and the active card is given a very high `z-index` and maybe `pointer-events: auto` while the world gets `pointer-events: none`.

## Un-zoom
- Clicking the backdrop should trigger the reverse animation.
- Save the "pre-zoom" `panX`, `panY`, `zoom` to return to them.
