# App

**Status:** Draft · **Prefix:** APP

## Purpose

The background instance, tray icon, global hotkeys, start at login and the settings that persist between captures.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| APP-1 | Starting the app with no arguments runs one background instance per user session; starting it again with no arguments exits at once with code 0 and leaves the first running. | start twice → one process, second exits 0 |
| APP-2 | Tray menu: capture region, capture full, capture display ▸ (one row per display: index, name, physical size), launcher, recent captures, open save folder, close all pins, settings, about, quit. `tray_click: capture\|menu` (capture) sets what a primary click on the icon does; a secondary click always opens the menu. | 2 displays → submenu with 2 rows; click icon → region overlay |
| APP-3 | `tray_icon` (default true): when false there is no tray icon and the instance keeps running, reachable by hotkeys and CLI. When the desktop has no tray area, the app behaves as if false and says so once. | tray off → no icon, hotkey still captures |
| APP-4 | Global hotkeys, configurable, defaults: region capture `PrintScreen` (Windows, Linux) or `Cmd+Shift+2` (macOS); full capture `Ctrl+PrintScreen` (macOS `Cmd+Shift+1`); recent captures `Shift+PrintScreen` (macOS `Cmd+Shift+0`). A hotkey another app or the OS holds (e.g. an OS screenshot tool on `PrintScreen`) is not registered: settings mark it `not registered`, explain how the user can free it, and the others still work; the app never changes OS settings. | another app holds PrintScreen → marked, full hotkey works |
| APP-5 | Wayland: the app cannot grab keys itself; it registers hotkeys through the desktop's global-shortcut service when present (the desktop may ask the user to confirm), otherwise settings show the CLI command to bind in the desktop's own shortcut settings. | Wayland without service → settings show `gui` command |
| APP-6 | `start_at_login` (default false) adds or removes a per-user login item; no administrator rights are needed. | turn on, log in again → instance running |
| APP-7 | Settings live in one per-user text file in the OS configuration folder. Unknown keys are ignored with a warning; an invalid value falls back to that key's default with a warning; the rest still load. | `dim_opacity=999` → default used, warning logged |
| APP-8 | Settings window sections: general, capture, tools, shortcuts, output, integrations ([PRIV-3](privacy.md)). Changes apply from the next capture with no restart. | change `save_format` to jpg → next save is jpg |
| APP-9 | Overlay shortcuts ([SEL](selection.md), [ANN-2](annotate.md), [EDT](editing.md)) are configurable; assigning a key already used by another overlay action is refused with a message naming that action; an empty key leaves the action unbound. | assign `R` to arrow → refused, names rectangle |
| APP-10 | `ui_language` (auto = OS language, falls back to English); `accent_color` (#RRGGBB) colours selection border, handles and toolbar. | accent `#00AA00` → green handles |

## Out of scope

- Themes beyond the accent colour; per-display settings.
- Self-update or installing packages.

## Decisions

- macOS defaults use `Cmd+Shift+0/1/2`, which the OS does not reserve (it uses 3, 4, 5 and 6).
- No idle auto-exit: the background instance stays until quit, so hotkeys always work.
