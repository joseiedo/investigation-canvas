# Phase 03 Research: SVG Connections

## Implementation Strategy

### SVG Layer Setup
- **Global SVG Container**: A `<svg>` element that fills the entire `#board` and sits behind the cards but above the background grain/vignette.
- **`pointer-events: none`**: Ensure the SVG layer itself doesn't intercept clicks, though individual `<path>` or `<circle>` elements can if needed (though not required for this phase).

### Connect Mode Toggle
- **Button UI**: A simple floating button in a corner (e.g., top-left) that toggles a `.connect-mode` class on the `#board`.
- **CSS Transitions**: When `.connect-mode` is active, the cursor could change to `crosshair` or `add` to signal that clicking a card will start a connection.

### Drawing Logic
- **Selection State**: Store the `firstCard` clicked in a variable. When a second card is clicked, create a connection object and add it to a `connections` array.
- **Connection Data**: Each connection stores the IDs or references to `fromCard` and `toCard`.
- **Center Point Calculation**:
  - `cardX = card.offsetLeft + card.offsetWidth / 2`
  - `cardY = card.offsetTop + card.offsetHeight / 2`

### Quadratic Bezier Sag (`Q`)
- **Control Point**: To simulate sag, the control point `(cx, cy)` should be halfway between the cards horizontally, but lower than both vertically.
  - `cx = (x1 + x2) / 2`
  - `cy = Math.max(y1, y2) + sagAmount` (where `sagAmount` is a fixed value like 20–40px).

### Real-Time Updates
- **`updateConnections()` function**: Iterate through the `connections` array and update the `d` attribute of the corresponding `<path>` elements in the SVG.
- **Trigger**: Call `updateConnections()` inside the `onMouseMove` function when a card is being dragged.

## Verification Strategy
- **Automated**: Use a script to simulate clicking two cards in connect mode and check if a `<path>` element is added to the SVG.
- **Manual**: Toggle connect mode, draw a line, and drag a card to see if the line stays attached and maintains its curve.
