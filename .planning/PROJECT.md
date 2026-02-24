# Investigation Canvas

## What This Is

A PoC of a detective investigation board for a game. Users can pin and drag rectangular evidence cards around a full-viewport board, connect them with red string lines (conspiracy board style), and click any evidence to open an expanded view for free-hand drawing and text annotation. Built with pure HTML, CSS, and JavaScript — no frameworks.

## Core Value

A tactile, movie-like investigation board where players can visually connect clues and scribble notes — the feel of a real detective's wall.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Full-viewport board as the canvas
- [ ] 5 pre-set rectangular/square evidence cards with pin aesthetic
- [ ] Drag evidences freely anywhere on the board
- [ ] Toggle "connect mode" to link two evidences with a red string line
- [ ] Click an evidence to expand it into a detail view
- [ ] In expanded view: free-hand drawing on the evidence (canvas)
- [ ] In expanded view: type and save a text annotation

### Out of Scope

- Images/photos as evidence content — rectangular placeholders only for this PoC
- Deleting connections — not needed for initial PoC
- Saving state to localStorage/backend — ephemeral only for now
- Zoom/pan of the board itself — full viewport, no pan needed for PoC
- Mobile/touch support — desktop mouse interaction only

## Context

- Pure HTML/CSS/JS implementation — no build tools, no frameworks, single-file or minimal files
- Detective/noir aesthetic expected: dark board (cork board or dark background), pinned cards look physical, red string connections
- This is a game asset PoC — the goal is to validate the feel and interaction model, not build production software

## Constraints

- **Tech Stack**: HTML + CSS + JS only — no npm, no React, no Canvas libraries
- **Scope**: PoC only — 5 fixed evidences (rectangles/squares), no dynamic add/remove
- **Delivery**: Single browser file or minimal file set, opens directly in browser

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Rectangles/squares only for evidences | Simplest shape to implement for PoC validation | — Pending |
| SVG for red string connections | SVG lines between DOM elements are clean and scalable | — Pending |
| Canvas inside expanded view for drawing | HTML Canvas API is native, no dependencies needed | — Pending |

---
*Last updated: 2026-02-24 after initialization*
