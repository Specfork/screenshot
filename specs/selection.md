# Selection

**Status:** Draft · **Prefix:** SEL

## Purpose

The interactive overlay: choosing and adjusting the area, the toolbar, and finishing or abandoning a capture.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| SEL-1 | The overlay shows the frozen image on every display. Outside the selection the image is dimmed by `dim_opacity` 0..255 (default 180); before any selection the whole image is dimmed and, when `show_help` is true (default), a short list of the main shortcuts is shown. | open overlay → help visible; select → only the outside dimmed |
| SEL-2 | Dragging with the primary button draws the selection; releasing finishes it. A drag smaller than 2×2 logical px counts as a click ([CAP-7](capture.md)). | drag 1 px → no selection |
| SEL-3 | A selection has 8 handles (corners and edge midpoints) to resize and an inside area to move; with `Shift` held a handle resizes symmetrically around the centre. The selection always stays inside the desktop. | drag the right handle with Shift +10 px → left edge moves −10 px |
| SEL-4 | Keyboard: arrows move the selection 1 logical px; `Shift`+arrow moves the right or bottom edge 1 px; `Ctrl+Shift`+arrow grows or shrinks both opposite edges 1 px each; `Ctrl+A` selects the display under the pointer, a second `Ctrl+A` the whole desktop. | Ctrl+A twice on 2 displays → desktop selected |
| SEL-5 | While a selection is drawn, moved or resized its size (physical px) and position are shown next to it and hidden `geometry_hide_ms` after the last change (0..10000, default 2000; 0 = always shown). | resize, wait 2 s → size label gone |
| SEL-6 | Magnifier, off by default (`show_magnifier`): a zoomed view of about 15×15 px around the pointer at 8× with a crosshair and the hex colour of the centre pixel. | turn on, hover a red pixel → shows `#FF0000` |
| SEL-7 | Once a selection exists, a toolbar of tools ([ANN](annotate.md)) and actions ([OUT](output.md)) appears beside it, outside when there is room, otherwise inside along an edge, always fully on one display. Which buttons appear and in what order is `toolbar_buttons`. | selection touching all display edges → toolbar inside the selection |
| SEL-8 | `Space` toggles a side panel with the current tool's options (colour, size, font) and the annotation list ([EDT-7](editing.md)). | press Space → panel shown; again → hidden |
| SEL-9 | `Esc` ends text editing when a text box is active; otherwise it cancels the capture. `Ctrl+Backspace` removes the selection and its annotations and keeps the overlay open. A cancelled capture writes, copies and pins nothing; a notification says so when `notify_on_cancel` is true (default false). | Esc with no text box → overlay closes, clipboard unchanged |
| SEL-10 | `Enter` accepts: runs the request's `actions`, or `default_accept_action: copy\|save\|copy_and_save\|pin` (default copy) when it has none. `Enter` with no selection accepts the whole display under the pointer. `Ctrl+C`, `Ctrl+S` and the pin action accept with that one action; with `accept_on_double_click` (default false) a double-click inside the selection accepts too. | no selection, Enter → clipboard holds that display |

## Out of scope

- Free-form (lasso) or multiple disjoint selections.
- Grid snapping and rulers.

## Decisions

- `Ctrl+A` escalates display → desktop (SEL-4) so one key covers both, instead of the source's single whole-desktop step.
- Upload is never a default accept action; see [PRIV-3](privacy.md).
