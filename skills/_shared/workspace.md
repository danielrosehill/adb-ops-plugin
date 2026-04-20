# adb-ops workspace conventions

All skills in this plugin read/write user state from a single workspace directory:

```
~/.claude/adb-ops/
├── profile.yaml          # phone model, Android version, rooted status, notes
├── mappings.yaml         # phone path -> local path mappings, with labels + use cases
├── bloatware-log.jsonl   # append-only log of package removals/restorations
└── devices/<serial>/     # per-device overrides (optional)
```

## profile.yaml schema

```yaml
device:
  serial: <adb serial>
  manufacturer: <e.g. Google>
  model: <e.g. Pixel 8>
  android_version: <e.g. 14>
  rooted: true | false
  notes: <free text>
captured_at: <ISO 8601>
```

## mappings.yaml schema

```yaml
mappings:
  - label: camera-photos
    phone_path: /sdcard/DCIM/Camera
    local_path: ~/Pictures/Phone/Camera
    use_case: "Import new camera photos after a trip"
    direction: pull         # pull | push | bidirectional
  - label: voice-memos
    phone_path: /sdcard/Recordings
    local_path: ~/Audio/Voice-Memos
    use_case: "Pull dictations for transcription"
    direction: pull
```

## bloatware-log.jsonl

Append-only newline-delimited JSON. One record per operation.

```json
{"ts":"2026-04-20T10:30:00+03:00","device":"<serial>","action":"uninstall","package":"com.example.bloat","user":0,"method":"pm uninstall --user 0","result":"success","notes":""}
```

Actions: `uninstall`, `disable`, `restore`, `enable`.

## Helpers

- Always `adb devices` first; if multiple devices, require `-s <serial>` and confirm with the user.
- Treat the workspace as the source of truth — re-read it each invocation rather than caching.
- If `~/.claude/adb-ops/` is missing, prompt the user to run the `onboard` skill first.
