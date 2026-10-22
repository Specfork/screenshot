# Capture

**Status:** Draft · **Prefix:** CAP

## Purpose

What can be captured, how a capture request is described, and what happens between the request and the image.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| CAP-1 | Modes: `region` (interactive overlay, [SEL](selection.md)), `full` (all displays in one image), `screen` (one display), `window` (one top-level window). Every mode can be started from a global hotkey, the tray menu, the CLI or the launcher, a small window to choose mode, delay (0..600 s), display and actions that hides itself before the delay starts. | start each mode from the CLI → image bounds equal region, desktop, display, window |
| CAP-2 | Capture request: `{mode, delay_ms 0..600000 (0), display (index or current), region (optional rectangle), use_last_region (false), include_cursor (false), actions: set of copy\|save\|pin\|print_raw\|print_geometry, save_path (optional), accept_on_select (false)}`. `include_cursor` composites the pointer image where the platform provides it, otherwise is ignored. A request with an invalid field is refused before anything is grabbed and the message names the field. | `delay_ms` -5 → refused, message names `delay_ms`, no image |
| CAP-3 | Delay: the screen is grabbed `delay_ms` after the request. Nothing from the app is visible on screen during the delay except a countdown in the tray icon tooltip; the request can be cancelled from the tray menu. | delay 3000, open a menu in another app → the menu is in the image |
| CAP-4 | Freeze: `region` and interactive `window` grab every display once when the overlay opens; the overlay and the result show that frozen image, not later screen changes. | video playing, start region → result shows the frame from the start |
| CAP-5 | `full` returns one image covering the bounding box of all displays ([DSP-1](displays.md)); parts of the box no display covers are transparent, or black in formats without alpha. | two displays offset vertically → empty corner is transparent in PNG |
| CAP-6 | `screen` captures the display given by `display` (index per [DSP-1](displays.md)) or, with `current` (default), the display containing the pointer. An index with no display → `no_such_display`, nothing captured. | 2 displays, `display` 5 → `no_such_display` |
| CAP-7 | Window selection: in the overlay, hovering highlights the window under the pointer and a click without dragging selects its bounds, including title bar and frame when `window_frame` is true (default true). Non-interactive `window` captures the active window. Where the platform does not expose windows ([DSP-8](displays.md)), `window` opens the region overlay with a notice instead. | X11, click a window → selection equals its outer bounds; Wayland → region overlay + notice |
| CAP-8 | Last region: every accepted region capture remembers its rectangle in global coordinates, in memory only unless `remember_last_region` is true (default false), then also across restarts. `use_last_region` preselects it; with none, or one that no longer intersects any display, the overlay opens with no selection. | capture 200×100 at 50,50, request again with `use_last_region` → same rectangle preselected |
| CAP-9 | `accept_on_select`: the capture completes when the first selection drag is released, with no annotation step; with no `actions` it saves ([OUT-2](output.md)). | drag 100×100 with the flag → file saved, overlay closed |
| CAP-10 | The app's own transient UI (tray menu, launcher, notifications, countdown) is hidden before the grab and never appears in the image. Pinned images ([PIN](pin.md)) stay visible unless `hide_pins_on_capture` is true (default false). | start capture from the tray menu → menu absent from the image |
| CAP-11 | At most one interactive overlay at a time unless `allow_multiple_captures` is true (default false); another interactive request while one is open focuses the open one and the new request ends as `cancelled`. Non-interactive requests are never blocked. | overlay open, press the hotkey again → still one overlay |
| CAP-12 | If the platform denies or cannot provide the screen image, no overlay opens, a notification gives the reason, and the request ends as `capture_denied` (permission refused) or `capture_unavailable` (no capture service). | permission refused → notification, CLI exit code 3 |

## Out of scope

- Scrolling or long-page capture, video or GIF recording, OCR.
- Capturing windows hidden behind others or minimised.

## Decisions

- Window capture (CAP-7) is new: the source has region, display and full capture only; hover-to-pick and active-window capture were designed here.
- The pointer can be included (`include_cursor`, CAP-2); the source never includes it.
