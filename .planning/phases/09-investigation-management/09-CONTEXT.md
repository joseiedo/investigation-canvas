# Phase 09 Context: Investigation Management

## Vision
An investigation is a dynamic process. Theories change, and connections that once seemed solid may later be proven false. This phase focuses on giving the user the tools to manage their board: renaming evidence to reflect new findings and deleting red strings that no longer make sense. These management tools transform the board from a static display into a flexible, evolving investigative environment.

## Essentials
- **Renameable Evidence**: Users can edit the title of any card directly from the expanded view.
- **Connection Deletion**: A way to remove specific red strings from the board.
- **Persistence**: All management actions (renames, deletions) must be saved to `localStorage` and restored on reload.
- **UX**: Renaming should feel like updating a file; deleting connections should be deliberate but easy (e.g., right-click).

## Boundaries
- **No Undo**: For this PoC, management actions are permanent (unless re-added).
- **Fixed Card Count**: We are still working with the 5 initial cards; only their content/connections are changing.
- **No Bulk Delete**: Connections are deleted one by one.
