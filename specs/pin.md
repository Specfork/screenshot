# Pin

**Status:** Draft · **Prefix:** PIN

## Purpose

Keeping a captured image floating above other windows as a reference.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| PIN-1 | Pin opens a borderless, always-on-top window showing the image at the selection's place and logical size; it has no taskbar or app-switcher entry. | pin 300×200 at 100,100 → window there, same size |
| PIN-2 | Dragging with the primary button moves the pin, across displays too; on a display with another scale it keeps its logical size. | drag from 1.0 to 2.0 display → same apparent size |
| PIN-3 | Zoom: wheel or pinch scales 10%..800% in 10% steps around the pointer; smooth scaling when `pin_smooth_zoom` is true (default), otherwise nearest-neighbour. | wheel up 3 times → 130% |
| PIN-4 | Opacity 10%..100% (default 100%): keys `1`..`9` set 10%..90%, `0` sets 100%; menu items raise or lower by 10% within that range. A pin can never become fully transparent. | press 3 → 30%; lower twice → 10% |
| PIN-5 | Secondary click opens a menu: copy, save, rotate left or right by 90° (the window takes the rotated size), raise opacity, lower opacity, close. Copy and save follow [OUT](output.md) using the original pixels, not the zoomed view. | zoom 200%, copy → original size image |
| PIN-6 | Close with `Esc` while the pin has focus, the menu, or "close all pins" in the tray menu. | 3 pins, close all → none left |
| PIN-7 | Pins live in memory only: quitting the app closes them and they are not restored on next start. | pin, quit, start → no pins |
| PIN-8 | Wayland: when the compositor does not honour placement or always-on-top, the pin opens where the compositor puts it and the app does not fake either. | Wayland → pin opens, may not stay on top |

## Out of scope

- Drawing on a pin; pinning images that were not captured (from file or clipboard).

## Decisions

- Zoom stops at 800% and opacity at 10% (PIN-3, PIN-4) so a pin can always be found and closed.
