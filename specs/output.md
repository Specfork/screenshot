# Output

**Status:** Draft · **Prefix:** OUT

## Purpose

What happens to a finished image: clipboard, file, file names and formats, notifications, handing to another app, and the list of recent captures.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| OUT-1 | Copy puts the image on the system clipboard as lossless PNG, plus the platform's native bitmap format where one exists. `clipboard_format: png\|jpeg` (png). | copy, paste into an image editor → pixel-identical |
| OUT-2 | Save with `save_path_fixed` false (default) opens a save dialog in `save_dir` (default: the user's Pictures folder, else home) with a name from `filename_pattern`; with true it writes there at once, no dialog. A cancelled dialog is a cancelled save, not an error. | fixed on → file appears with no dialog |
| OUT-3 | `filename_pattern` (default `screenshot_%Y-%m-%d_%H-%M-%S`) expands `%Y %m %d %H %M %S %j %a %b %%` from the local capture time. Characters invalid on any supported OS (`/ \ : * ? " < > \|` and control characters) become `-`; a trailing lone `%` is dropped; an empty result uses the default. | `shot %H:%M` at 09:05 → `shot 09-05.png` |
| OUT-4 | `save_format: png\|jpg\|webp\|bmp` (png) sets the extension. A path given to a file keeps its extension when it is one of these, otherwise the request fails with `unsupported_format`. Quality 1..100 for jpg and webp (`jpeg_quality`, default 90); png and bmp are lossless. The file carries DPI per [DSP-3](displays.md) and no other metadata ([PRIV-9](privacy.md)). | `-p ~/a.tiff` → `unsupported_format` |
| OUT-5 | Saving never overwrites silently: without a dialog, an existing name gets `_1`, `_2`, … (first free) before the extension; in the dialog the OS overwrite confirmation applies. | `a.png` exists → `a_1.png` written |
| OUT-6 | `copy_path_after_save` (false): after a save, the clipboard holds the absolute path as text instead of the image. `save_after_copy` (false): every copy also saves to `save_dir` without a dialog. | `save_after_copy` on, Ctrl+C → clipboard image and file both exist |
| OUT-7 | Several actions on one request run in the order copy, save, pin, print; a failing one (no permission, disk full) does not stop the rest, is reported, and the request ends as `output_failed`. | read-only `save_dir`, copy+save → clipboard filled, save error reported |
| OUT-8 | Notifications (`notifications`, default true): after save, the full path, and clicking opens the containing folder; after copy, a short confirmation; after a failure, the reason. Notifications never show the image or its text content. | save → notification with path, no preview image |
| OUT-9 | Open with (`Ctrl+O`): the OS chooser of apps that open images receives a temporary PNG; that file lives in the user's private temp folder and is deleted when the app quits or after 24 h, whichever comes first. | open with viewer, quit app → temp file gone |
| OUT-10 | Clipboard ownership: where clipboard data disappears when its owner exits (X11, Wayland), the app keeps serving the copied image until another app takes the clipboard, even when the copy came from a CLI call. | `full -c`, CLI returns, paste → image pastes |
| OUT-11 | Recent captures: a list of the last `recent_max` saved images (0..100, default 10; 0 = off), each with path, time and a thumbnail ([PRIV-7](privacy.md)); opened from the tray or `Shift+PrintScreen`. Selecting one copies that file's image again; a missing file is shown as missing. | save 3, open recent → 3 newest first |

## Out of scope

- Printing to a printer, emailing, sharing sheets beyond the OS "open with".
- Editing settings of image formats beyond quality.

## Decisions

- The default file name uses full date and time with seconds, so two quick captures rarely need OUT-5 numbering.
- Recent captures list only saved files (OUT-11); images that were only copied are never retained.
