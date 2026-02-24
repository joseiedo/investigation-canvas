# Phase 1: Static Board Layout - Context

**Gathered:** 2026-02-24
**Status:** Ready for planning

<domain>
## Phase Boundary

Full-viewport cork board detective wall with 5 hard-coded evidence cards pinned to it. The board fills the entire browser window with no scrollbars. Cards are static (no interaction) — dragging, connections, and click-to-expand are separate phases.

</domain>

<decisions>
## Implementation Decisions

### Card visual style
- Base material: index card / notecard style
- Color: all cream/off-white (uniform — no color variation between cards)
- Each card has a slight random tilt (±2–5°) to feel physically hand-pinned
- Pin: round thumbtack head (red or colored) as a CSS pseudo-element at top center of each card
- Strong drop shadow (high offset, soft blur) to give cards lift off the board

### Board atmosphere
- Background tone: dark, moody cork (deep brown/charcoal) — high contrast against cream cards
- Texture: CSS noise pattern using radial-gradients and grain — no image assets
- Ambient elements: edge vignette (darkened corners/edges) + subtle film grain overlay on the board
- Overall feel: cinematic thriller, not a generic webpage

### Card positioning
- Cards are naturally scattered at varied positions — no grid, no obvious pattern
- Minor overlaps between cards are allowed (as long as titles remain readable)
- Positions defined as percentages (viewport-relative) so layout adapts to window size

### Card content
- 5 cards with generic detective case labels (e.g., "Suspect A", "Victim", "Crime Scene", "Alibi", "Witness")
- Each card shows title only — no body text, no tags
- Font: typewriter / monospace (Courier-style) — cleanly typeset, not irregular

### Claude's Discretion
- Exact percentage coordinates for each card's initial position
- Specific CSS technique for noise/grain texture
- Pin pseudo-element sizing and exact color shade (red family)
- Card dimensions (width × height ratio appropriate for an index card)
- Exact shadow values within the "strong/dramatic" direction

</decisions>

<specifics>
## Specific Ideas

- The aesthetic is "detective case board" — cinematic, dark, tactile. Each design choice should reinforce that feel.
- Cards should read as physically pinned paper on a real board, not UI elements on a webpage.

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 01-static-board-layout*
*Context gathered: 2026-02-24*
