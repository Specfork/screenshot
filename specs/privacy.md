# Privacy

**Status:** Draft · **Prefix:** PRIV

## Purpose

Screenshots often hold private data. These rules keep images on the device unless the person sends them, make redaction safe, and bound what is kept.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| PRIV-1 | A fresh install makes no network request at all: no telemetry, crash reports, update checks, or downloads. | fresh install, capture/copy/save with traffic monitor → zero requests |
| PRIV-2 | `check_for_updates` (default false): when on, at most one request a day, fetching only the latest version number and sending nothing but the app version. | turn on → one request per day, no image data |
| PRIV-3 | Upload is an opt-in integration: there is no upload button, shortcut or CLI flag until the person adds an upload target `{name, endpoint, auth}` in settings. No target is built in or preconfigured; `Enter` never uploads. | fresh install → no upload control anywhere |
| PRIV-4 | Each upload shows a confirmation with a preview and the destination host unless that target's `upload_without_confirmation` is true (default false). The CLI uploads only with an explicit `--upload <target name>`. The returned link, and the deletion link when the service gives one, are shown and kept in a local upload list that can be cleared at once. | upload → dialog shows host; cancel → no request; clear list → empty |
| PRIV-5 | Upload credentials are stored in the OS credential store; without one, in a separate file readable only by the user, with a warning. They never appear in the settings file or logs. | add target → settings file has no token |
| PRIV-6 | Images reach disk only through save, `save_after_copy`, open with ([OUT-9](output.md)) or recent-capture thumbnails; the frozen screen and annotations are held in memory only and dropped when the overlay closes. | copy only → no new image file anywhere |
| PRIV-7 | Recent-capture thumbnails are at most 256 px on the longest side, kept only for saved images, capped at `recent_max`, stored in the user cache folder readable only by the user; "clear recent" deletes all of them, and `recent_max` 0 deletes them and stores no more. | set 0 → thumbnail folder empty |
| PRIV-8 | Redaction is safe by default: pixels inside a redact rectangle never influence its output; pixelate and blur derive colour only from pixels just outside its edges, solid uses the draw colour. `insecure_redaction` (default false) allows true pixelate or blur of the inside. | two captures differing only inside the rectangle → identical output pixels |
| PRIV-9 | Saved files contain pixels, DPI and an sRGB tag only: no user name, host name, window titles, app version, location or capture time in metadata. | save, read metadata → only DPI and colour profile |
| PRIV-10 | `mark_copy_sensitive` (default false): when true, copies carry the platform's "do not keep in clipboard history" marker so clipboard managers skip them. | on, copy → clipboard history tool ignores it |
| PRIV-11 | Logs hold errors and warnings only, never image data, clipboard content, window titles or saved file names. | trigger a save error → log has reason, no file name |

## Out of scope

- Built-in upload providers, cloud accounts, sync of settings.
- Detecting sensitive content (faces, cards, passwords) automatically.

## Decisions

- No upload target ships preconfigured (PRIV-3); the source ships one image host with a built-in client key and binds a default key to it.
- Update checks are off by default (PRIV-2); the source turns them on.
- Safe redaction (PRIV-8) follows the source's default of sampling only the area's border.
