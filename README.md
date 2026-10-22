---
specfork: 1
name: "@specfork/screenshot"
summary: Local desktop screenshots of a region, display, window or all displays, with delay, HiDPI output, annotation, undo, copy, save, pin, hotkeys, tray and CLI; no network by default.
license: CC-BY-4.0
tags: [desktop, productivity]
derived_from:
  repo: https://github.com/flameshot-org/flameshot
  commit: 2cd9ad7849d98092684bfbedead413a019c7dc75
  url: https://github.com/flameshot-org/flameshot
  license: GPL-3.0-or-later
---

# Screenshot

One person takes screenshots on their own computer. A hotkey, the tray icon or a command starts a
capture of a region, a whole display, all displays or one window, optionally after a delay. In the
interactive mode the frozen screen is shown, the person selects an area, marks it up with arrows,
shapes, text, highlights, counters and redactions, then copies, saves or pins the result. Everything
happens on the device; nothing is uploaded unless the person sets up an upload target themselves.
Behaviour derived from Flameshot's open-source code (GPL-3.0-or-later), written fresh and stack-free.
Not affiliated with or endorsed by the Flameshot project (flameshot-org). The name is used only to credit the source.

| Area | Prefix | Covers |
|---|---|---|
| [capture.md](specs/capture.md) | CAP | Capture modes, request fields, delay, freeze, last region, cursor, failures |
| [displays.md](specs/displays.md) | DSP | Multi-monitor layout, HiDPI and mixed scaling, X11, Wayland, Windows, macOS |
| [selection.md](specs/selection.md) | SEL | Overlay, selecting and adjusting the area, toolbar, accept, cancel |
| [annotate.md](specs/annotate.md) | ANN | Arrow, line, rectangle, ellipse, pencil, marker, text, redact, counter, colour, size |
| [editing.md](specs/editing.md) | EDT | Undo/redo, selecting, moving, restyling and ordering annotations |
| [output.md](specs/output.md) | OUT | Clipboard, save, filename pattern, formats, notifications, open with, recent captures |
| [pin.md](specs/pin.md) | PIN | Images pinned on top of the desktop |
| [app.md](specs/app.md) | APP | Background instance, tray, global hotkeys, autostart, settings |
| [cli.md](specs/cli.md) | CLI | Commands, options, exit codes, local command interface |
| [privacy.md](specs/privacy.md) | PRIV | No network by default, opt-in upload, redaction safety, local caches, metadata |

Single user, no account, no server. Runs on Windows, macOS and Linux (X11 and Wayland); platform
differences are stated as behaviour. Shortcuts are defaults and configurable; `Ctrl` means `Cmd` on
macOS. Coordinates are logical pixels in the global desktop space unless a rule says physical pixels;
setting and field names are `snake_case`.

This spec is licensed under Creative Commons Attribution 4.0 International (CC BY 4.0):
https://creativecommons.org/licenses/by/4.0/legalcode

It describes behaviour of Flameshot (https://github.com/flameshot-org/flameshot, GPL-3.0-or-later)
in new words; no code or text was copied.
See README.md front matter `derived_from`.
