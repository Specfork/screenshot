# Displays

**Status:** Draft · **Prefix:** DSP

## Purpose

How several displays, display scaling and the different desktop platforms shape what is captured and at what resolution.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| DSP-1 | Each display: `{index, name, x, y, width, height (logical px), scale (≥ 1.0, e.g. 1.25, 2.0), primary}`. The desktop is the bounding box of all displays in one global logical coordinate space. Indexes are 0-based, ordered by `x` then `y` of the top-left corner, and stay the same while the layout does. | 1920×1080 at 0,0 and 2560×1440 at 1920,0 → desktop 4480×1440; display at x=-1920 → index 0 |
| DSP-2 | Images use physical pixels: an area of w×h logical px on a display with `scale` s yields round(w·s)×round(h·s) pixels, never downscaled to logical size. | 100×50 on a 2.0 display → 200×100 image |
| DSP-3 | An area spanning displays with different scales is produced at the highest scale among the displays it touches; parts from lower-scale displays are upscaled. The saved image records DPI = 96 × that scale. | span 1.0 and 2.0 displays → whole image at 2.0, 192 DPI |
| DSP-4 | Region values are logical: `WxH+X+Y` in global coordinates, `screenN` for display N's bounds, `all` for the desktop. A region outside every display → `invalid_region`. | `400x300+10+10` on a 2.0 display → 800×600 image |
| DSP-5 | The overlay covers every display at once, each drawn at its own scale so UI stays sharp, and a selection may cross display edges. | drag from display 0 into display 1 → one selection |
| DSP-6 | The display layout is read when each capture starts; a display added, removed or rescaled while the overlay is open cancels the capture with notice `display_changed`. | unplug a display during selection → overlay closes, notice shown |
| DSP-7 | Wayland: the grab goes through the desktop's screenshot permission service. The desktop may show its own consent dialog, first time or every time; the app never bypasses it. No such service → `capture_unavailable` ([CAP-12](capture.md)). | Wayland desktop without the service → no overlay, `capture_unavailable` |
| DSP-8 | Wayland: when window lists, global pointer position or the active window are not available, `current` means the primary display, interactive `window` falls back to region ([CAP-7](capture.md)) and non-interactive `window` → `capture_unavailable`. | Wayland, `screen` with `current` → primary display |
| DSP-9 | macOS: capturing needs the OS screen-recording permission. Without it the app captures nothing (never a blank image), explains how to grant it, and the request ends as `capture_denied`. X11 and Windows: displays are read directly with no consent dialog, also on X11 without a compositor or screenshot service. | macOS permission off → no file, `capture_denied`; X11 minimal window manager → capture works |

## Out of scope

- Per-display colour profiles and HDR tone mapping; images are 8-bit sRGB.
- Virtual desktops or workspaces other than the visible one.

## Decisions

- Display order is defined by position (DSP-1) rather than by the platform's list order, so `display 1` means the same screen on every OS.
- Mixed-scale areas use the highest scale (DSP-3) so no detail is lost; the source behaves this way on macOS only.
