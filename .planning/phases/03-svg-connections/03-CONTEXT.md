# Phase 03 Context: SVG Connections

## Vision
The "red string" is the quintessential detective wall trope. Users should be able to enter a "Connect Mode" and draw lines between evidence cards. These lines must feel physical — they should sag slightly under gravity rather than being perfectly straight, and they must stay attached to the cards even when they are dragged to new positions.

## Essentials
- **Connect Mode Toggle**: A clear UI element to switch between "Move" and "Connect" behaviors.
- **Dynamic Drawing**: Lines must update in real-time as cards move.
- **Visual Sag**: Use SVG Quadratic Bezier curves (`Q`) to simulate gravity.
- **Persistent State (Session)**: Connections made should remain until the page is reloaded.

## Boundaries
- **No Deleting**: Deleting connections is out of scope for the PoC.
- **Red Color Only**: Single color for simplicity.
- **Manual Toggle**: User must explicitly enter connect mode to draw.
- **Card-to-Card Only**: Connections only start/end at card centers (or relative offsets).
