# Annotate

**Status:** Draft · **Prefix:** ANN

## Purpose

Drawing tools used on the selection before it is exported, and the colour and size every tool shares.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| ANN-1 | Annotation: `{id, tool, color (#RRGGBB), opacity 0..255, size 1..100, points[], fill: none\|solid, text, font, z}`. Annotations stay editable objects until export; the exported image is the frozen image plus all annotations drawn in `z` order, clipped to the selection. | draw arrow, move it, export → arrow at its new place |
| ANN-2 | Tools and default keys: arrow `A`, line `L`, rectangle `R`, ellipse `E`, pencil `P`, marker `M`, text `T`, redact `B`, counter `N`, pointer `V` ([EDT-5](editing.md)). Tools are usable only while a selection exists; a key with no selection does nothing. | no selection, press R → no tool change |
| ANN-3 | Line: a straight segment from press to release, `size` = stroke width in logical px; with `Shift` the angle snaps to multiples of 45°. | Shift-drag at 40° → drawn at 45° |
| ANN-4 | Arrow: a line with a filled head at the release point; head length = max(10, 3 × size) logical px; `arrow_heads: end\|both` (end). Shift snaps as ANN-3. | size 5 → head length 15 px |
| ANN-5 | Rectangle: from press to release corner, corner radius 0..50 px (0). Ellipse: inscribed in the dragged box. Both are an outline of width `size`, or filled when `fill` is solid; `Shift` keeps a square or circle. | Shift-drag 100×60 → 100×100 square |
| ANN-6 | Pencil: a smoothed freehand stroke following the pointer, width `size`. | drag a loop → loop drawn |
| ANN-7 | Marker: a highlighter stroke at 40% opacity whose overlapping parts within one stroke do not darken; freehand, or straight with `Shift`; default size 14. | scribble over the same spot twice in one stroke → uniform tint |
| ANN-8 | Text: a click opens a text box at that point; typing inserts text, `Enter` adds a line, `Ctrl+Enter` or a click outside commits. Fields: font family (system UI font), size 6..200 pt (16), bold, italic, underline, strike-through, align `left\|center\|right`. A box committed empty is discarded. | type nothing, click outside → no object |
| ANN-9 | Redact: a dragged rectangle obscured by `redact_mode: pixelate\|blur\|solid` (pixelate) at strength 1..10 (5). Output follows [PRIV-8](privacy.md): by default nothing inside the rectangle influences the result. | redact a word, export twice with different words underneath → identical pixels |
| ANN-10 | Counter: a click places a round badge with the next number in this capture, starting at 1; diameter = 16 + 4 × size px; digits black or white, whichever contrasts more with the badge colour; outline ring when `counter_outline` is true (default true). Deleting a counter renumbers all higher ones down by 1; undoing the delete restores every number; a new badge takes the highest number + 1. | 3 clicks → 1, 2, 3; delete 2 → 1, 2; undo → 1, 2, 3 |
| ANN-11 | Colour: default `#FF0000`; a palette of `palette_colors` (default 10 presets) plus a custom picker; a secondary click opens the palette at the pointer; `G` then a click sets it to the pixel under the pointer. The last colour is kept across captures. | pick blue, new capture → blue selected; G, click a green pixel → green |
| ANN-12 | Size: mouse wheel or `+`/`-` changes the current tool's size by 1 within 1..100 and shows the value briefly. Each tool keeps its own last size across captures; defaults: line, arrow, rectangle, ellipse, pencil 3; marker 14; text 16 pt; redact 5; counter 2. | pencil 3, wheel up twice → 5; marker still 14 |

## Out of scope

- Image stickers, emoji, shapes beyond those listed, colour inversion, crop or rotate inside the overlay.
- Per-point pressure from pen tablets.

## Decisions

- Default keys are our own (line `L`, ellipse `E`, counter `N`, pointer `V`) to read as the tool's initial where possible.
- Constraining uses `Shift` everywhere (ANN-3..7), the common convention in drawing apps.
