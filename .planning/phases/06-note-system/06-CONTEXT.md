# Phase 06 Context: Note System

## Vision
Every detective needs a place to record their theories. While drawing on evidence is visceral, typing detailed notes is essential for tracking complex leads. The Note System should feel integrated into the "expanded view" experience, allowing the user to study a piece of evidence and immediately jot down findings.

## Essentials
- **Typewriter Aesthetic**: Use monospace fonts and a minimalist design for the note field.
- **Auto-Save**: To ensure a low-friction experience, notes should save automatically as the user types (using `input` or `change` events).
- **Persistence**: Notes must survive modal closure and browser refreshes.
- **Contextual**: Each piece of evidence has its own unique, private note field.

## Boundaries
- **Simple Text**: No rich-text editing (bold, italics, etc.) needed for this PoC. Plain text in a `textarea` is sufficient.
- **Fixed Position**: The note field sits below the evidence in the expanded view.
- **No Export**: Exporting notes as a separate file is out of scope; they live on the board.
