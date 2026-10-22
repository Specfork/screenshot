# Editing

**Status:** Draft · **Prefix:** EDT

## Purpose

Changing annotations after they are drawn: undo and redo, selecting, moving, restyling, deleting and ordering.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| EDT-1 | Each completed change is one undo step: add, delete, move, resize, restyle, commit of a text edit, z-order change. Selection rectangle changes are not undo steps. Undo `Ctrl+Z`; redo `Ctrl+Shift+Z` or `Ctrl+Y`. | draw 2 rectangles, Ctrl+Z → 1 left; Ctrl+Y → 2 |
| EDT-2 | `undo_limit` 1..1000 (default 100): beyond it the oldest step is dropped and can no longer be undone. | limit 2, draw 3, undo 3 times → first drawing remains |
| EDT-3 | Any new change after an undo discards the steps that could have been redone. | draw A, B; undo; draw C; redo → nothing happens, A and C shown |
| EDT-4 | The history belongs to one overlay and is gone when the capture is accepted or cancelled. | accept, new capture, Ctrl+Z → nothing to undo |
| EDT-5 | Pointer tool (`V`): a click on an annotation selects it and shows its bounds; drag moves it; arrows nudge it 1 px (`Shift` 10 px); `Delete` or `Backspace` removes it; double-clicking a text annotation reopens it for editing. A click on empty space deselects. | select arrow, Delete → arrow gone, one undo step |
| EDT-6 | With an annotation selected, changing colour, opacity, size, fill or font applies to that annotation only, as one undo step, and also becomes the tool's current setting. | select red rectangle, pick green → that rectangle green |
| EDT-7 | Annotation list in the side panel ([SEL-8](selection.md)): one row per annotation, topmost first, showing tool and counter number or first text characters; selecting a row selects the annotation; move up/down changes `z`. | move bottom rectangle up → it draws over the arrow |
| EDT-8 | Moving or resizing the selection does not move annotations: they keep their place on the frozen image and are clipped to the new selection on export. | annotate, move selection 50 px right → annotation stays, partly clipped |

## Out of scope

- Grouping, aligning or copying annotations; editing annotations after export.

## Decisions

- Selection changes stay out of the undo history (EDT-1) so undo never makes the area jump unexpectedly.
