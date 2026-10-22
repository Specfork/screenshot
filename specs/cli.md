# CLI

**Status:** Draft · **Prefix:** CLI

## Purpose

Starting captures and changing settings from a terminal or script, and the minimal local command interface behind it.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| CLI-1 | Commands: `gui` (region overlay), `full`, `screen`, `window`, `launcher`, `config`, plus `--help` and `--version`. An unknown command or option prints usage to stderr and exits 2. | `screenshot frobnicate` → usage, exit 2 |
| CLI-2 | Capture options: `-p/--path <dir or file>`, `-c/--clipboard`, `-d/--delay <ms>`, `--region <WxH+X+Y\|screenN\|all>`, `--last-region`, `-r/--raw`, `-g/--print-geometry`, `--pin`, `-s/--accept-on-select`, `-n/--display <n>`, `--cursor`, `-e/--edit`, and `--upload <target>` only while an upload target exists ([PRIV-4](privacy.md)). Each maps to a [CAP-2](capture.md) field. | `gui -d 2000 -c` → overlay after 2 s; accept → copied |
| CLI-3 | Which command takes which option: `gui` — path, clipboard, delay, region, last-region, raw, print-geometry, pin, accept-on-select, cursor; `full` — path, clipboard, delay, raw, cursor; `screen` — display, path, clipboard, delay, region, raw, pin, edit, cursor; `window` — path, clipboard, delay, raw, pin, cursor. Others → exit 2. | `full --pin` → exit 2 |
| CLI-4 | Validation before any capture: delay integer 0..600000; path names an existing folder, or a file whose parent folder exists; region per [DSP-4](displays.md); display ≥ 0. A failure names the option and exits 2. | `-p /no/such/dir` → exit 2, names `--path` |
| CLI-5 | `full`, `screen` and `window` with none of `-c`, `-p`, `-r`, `--pin` save to `save_dir` with `filename_pattern`, no dialog. `-e` opens the overlay on the chosen display instead. | `full` → new file in `save_dir` |
| CLI-6 | `-r` writes the PNG bytes to stdout and nothing else there; `-g` writes `WxH+X+Y` and a newline (logical, global). With both, only `-r` output is written. | `gui -g`, select 10×10 at 5,5 → `10x10+5+5` |
| CLI-7 | The CLI hands the request to the running instance, starting a temporary one when none runs, waits for the result and exits: 0 done, 1 cancelled, 2 usage error, 3 `capture_denied`/`capture_unavailable`/`no_such_display`, 4 `output_failed`. Output and exit code reach the calling terminal on every OS, Windows included. | Esc in overlay → exit 1 |
| CLI-8 | `config` with no option opens the settings window. Options: `--autostart <bool>`, `--tray <bool>`, `--notifications <bool>`, `--filename-pattern <pattern>` (prints a sample name for now), `--accent <colour>`, `--check` (validates the settings file, lists problems, exits 1 if any). `<bool>` is exactly `true` or `false`; `<colour>` is `#RGB`, `#RRGGBB` or a CSS colour name. | `config --filename-pattern 'a_%Y'` → prints `a_2026` |
| CLI-9 | Command interface: the instance accepts the same requests over a local channel reachable only by the same OS user (per-user socket, named pipe or session bus). It never opens a network port. | other OS user sends a request → refused; port scan → no listener |

## Out of scope

- A scripting or plugin language; requests beyond CAP-2 fields and `config`.

## Decisions

- One exit-code table (CLI-7) for every command so scripts can tell user cancel from failure.
- `window` is a CLI command of its own, added with [CAP-7](capture.md).
