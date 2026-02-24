# Feature Landscape

**Domain:** Detective investigation board / conspiracy wall UI for a board game
**Researched:** 2026-02-24
**Confidence:** MEDIUM-HIGH (training knowledge on well-established media trope)

---

## Reference Sources

- **Film/TV physical boards**: True Detective S1 (Rust Cohle's wall), It's Always Sunny "Pepe Silvia", Homeland, The Wire, A Beautiful Mind, Mindhunter
- **Video games**: L.A. Noire, Return of the Obra Dinn, Disco Elysium, Her Story, Sherlock Holmes: Crimes & Punishments, Pentiment
- **Digital tools emulating the look**: Milanote, Miro conspiracy-board templates, Obsidian Canvas

---

## Table Stakes

Features users expect. Missing = product loses the "detective board" identity.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Cork/dark textured board background | Every reference uses a physical surface — cork, chalkboard, or dark wall. Without it, cards feel like a generic kanban. | Low | CSS background-image or CSS texture. Cork tan or dark felt. |
| Pinned evidence cards with pin aesthetic | A thumbtack/pushpin at top center is the universal signal. | Low | CSS pseudo-element or small SVG pin at card top. |
| Freely draggable cards | Fixed positions destroy the "I'm arranging my evidence" agency. | Medium | mousedown/mousemove/mouseup. Absolute positioning on the board. |
| Red string / thread connections | The single most iconic element. No strings = not a conspiracy board. | Medium | SVG line/path overlaid on the board, updated on card drag. |
| String follows card during drag | If you drag a card and the string stays behind, the illusion breaks. | Medium | Recalculate SVG endpoints on every mousemove during drag. |
| Cards have visible content area | Each card needs a face — title + identity. | Low | Card body with title. Slightly rotated for physicality. |
| Click-to-inspect / expanded view | The implicit promise: you can "pick up" any card and examine it closely. | Medium | Overlay/modal showing the card fullscreen with detail content. |
| Board fills the entire viewport | Scrollbars or constrained areas kill the immersion. | Low | width: 100vw; height: 100vh; overflow: hidden; |
| Slight card rotation / imperfect placement | Physical cards are never perfectly aligned. Small random rotations make the board feel lived-in. | Low | CSS transform: rotate() with a small random offset per card. |

---

## Differentiators

Features that set this product apart. Not expected by default, but significantly increase immersion.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| Free-hand drawing on individual cards | Lets players scrawl circles, arrows, underlining — exactly what detectives do. Turns static cards into annotated artifacts. | Medium | HTML Canvas inside expanded view; mousedown/mousemove to draw paths. |
| Text annotation on cards | Handwritten-style notes feel authentic. Players record their own theories. | Low-Medium | Textarea in expanded view, saved to card state. Use monospace or handwriting-style font. |
| String connection mode toggle | Prevents accidental connections while dragging. Mimics reaching for a spool of thread. | Low | Toggle button; cursor changes to crosshair; click card A then card B. |
| Weathered / aged card aesthetic | Old photographs and newspaper-clipping look heightens the noir period feel. | Low | CSS filter (sepia, yellow tint), paper texture on cards. |
| String sag / catenary curve | Real string sags under gravity. A curved SVG path adds one level of physical authenticity. | Low-Medium | SVG quadratic bezier (Q command) with downward control point. |
| Multiple string colors or line styles | Different thread colors encode relationship types (red = related, blue = timeline, yellow = suspect). | Medium | Color picker or preset palette. Requires connection metadata. |
| Subtle ambient animations | Slight card flutter or string sway maintains the living-wall feeling at idle. | Low-Medium | CSS keyframe on card transform (subtle oscillation). Limit to 1-2 cards for performance. |
| Card "face-down" state | Hidden evidence not yet found — a core mechanic in mystery board games. | Medium | CSS 3D card flip animation. Requires face-up/face-down state. |
| Magnifying glass cursor in inspect mode | Classic detective UX signal when hovering over a card. | Low | Custom CSS cursor with magnifying glass SVG. |
| Polaroid photo style cards | Square cards with white border and bottom label area — the most iconic evidence form. | Low | White border, taller bottom margin, title in bottom strip. |
| Sound design | Paper shuffle on drag, string snap on connection, pen scratch on annotation. | Medium | Web Audio API or short audio clips. High effort-to-reward; skip for PoC. |
| Sticky notes as a secondary card type | Yellow notes for theories vs. formal evidence cards — mirrors real detective boards. | Medium | Requires card type system; two CSS card variants. |

---

## Anti-Features

Features to explicitly NOT build for this PoC.

| Anti-Feature | Why Avoid | What to Do Instead |
|--------------|-----------|-------------------|
| Dynamic add/remove of cards | Doubles interaction surface; distracts from validating core feel. | Fix 5 pre-set cards. |
| Persistent state (localStorage / backend) | Engineering concern, not a feel concern. | Ephemeral-only. Refresh resets. |
| Zoom and pan of the board | Significant complexity; not needed if 5 cards fit in viewport. | Full-viewport, fixed positions. |
| Mobile / touch support | Parallel interaction model adds scope. Desktop mouse-first is correct. | Mouse-only. |
| Multi-user / collaborative editing | Production concern entirely out of PoC scope. | Single-player, local only. |
| Undo / redo | Requires command-pattern architecture. Overkill for 5 fixed cards. | Accept imperfect; refresh resets. |
| Deleting connections | Adds UI complexity (selecting a line) for minimal PoC value. | Connections are permanent once drawn. |
| Rich media evidence (photos, videos) | Production-game territory, not a board feel PoC. | Rectangular placeholders with labels only. |
| Custom card shapes (non-rectangular) | CSS complexity with minimal feel gain. | Rectangle/square + pin + rotation is enough. |
| Categorized evidence types with icons | Icon system and taxonomy is product design work beyond PoC scope. | Plaintext labels only. |

---

## Feature Dependencies

```
Drag-anywhere cards
  -> String connections must update on drag (endpoints follow card position)
  -> String endpoints anchored to card center or top-pin position

SVG overlay layer
  -> Must sit above board background but below cards (pointer-events: none on SVG)
  -> Z-index layering: background < SVG strings < cards < expanded view overlay

Connect mode toggle
  -> Requires tracking "first card selected" state
  -> Requires visual feedback on first card (highlight/glow) while awaiting second click
  -> String drawn after second card is clicked

Expanded view / inspect modal
  -> Triggered by card click — must NOT trigger during drag (distinguish click from drag)
  -> Contains: card title, free-hand Canvas, text annotation area
  -> Canvas drawing requires its own mousedown/mousemove/mouseup inside the modal

Free-hand drawing (inside expanded view)
  -> HTML Canvas element sized to fill expanded view area
  -> Draw path: mousedown starts path, mousemove adds points, mouseup ends path
  -> Drawing state is per-card (each card has its own canvas data)

Text annotation (inside expanded view)
  -> Stored per-card in JavaScript card state object
  -> Survives expanded view close/reopen within session (no persistence across refresh)

Card rotation aesthetic
  -> Applied at render time via CSS transform
  -> Does NOT affect drag hit area — use getBoundingClientRect() for rotated card position
```

---

## MVP Recommendation

Prioritize:
1. Board background with cork/dark texture — sets the scene immediately
2. Pinned cards with slight rotation — establishes the physical metaphor
3. Drag to reposition — the core interaction
4. Red string connections via SVG — the single most iconic element; without this it is just a kanban
5. String updates on drag — completeness of the string mechanic
6. Expanded view on card click — the "pick it up and examine" payoff
7. Free-hand drawing in expanded view — the most differentiating tactile feature
8. Text annotation in expanded view — rounds out the "annotating evidence" fantasy

Defer:
- Sound design: High effort, adds nothing to validating the board feel
- Card face-down state: Game mechanic complexity, not board feel complexity
- Multiple string colors: One color is enough for feel validation
- Sticky notes: Secondary card type; single card type is fine for PoC

---

## Sources

- Training knowledge: True Detective S1, It's Always Sunny "Pepe Silvia", Homeland, A Beautiful Mind (physical board references)
- Training knowledge: L.A. Noire, Return of the Obra Dinn, Disco Elysium, Sherlock Holmes: Crimes & Punishments (game investigation UI references)
- Training knowledge: Milanote, Miro, Obsidian Canvas (digital tools emulating physical board aesthetic)
- Confidence: MEDIUM-HIGH — the detective board trope is extremely well-codified in media; table stakes draw on consistent elements across 20+ examples.
