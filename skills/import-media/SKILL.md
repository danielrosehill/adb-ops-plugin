---
name: import-media
description: Import media (photos, videos, voice memos, downloads) from the connected Android device to the local computer using saved mappings from ~/.claude/adb-ops/mappings.yaml. Supports "import new since last run" (incremental) or full sync. Use when the user says "import my photos", "pull new videos from my phone", or references a mapping label like "import camera-photos".
---

# adb-ops: import-media

Pull files from the phone to the local computer using pre-configured mappings.

## Prerequisites

- `~/.claude/adb-ops/mappings.yaml` must exist with at least one mapping whose `direction` is `pull` or `bidirectional`. If missing, tell the user to run the `onboard` skill.

## Procedure

1. **Resolve target mapping(s)**:
   - If the user names a label (e.g. "camera-photos"), use that mapping.
   - If the user says "photos" / "videos" / "downloads" generically, pick the best-fit label (fuzzy match on label + use_case).
   - If ambiguous or the user says "import everything", list all pull mappings and ask which to run, or confirm "all of them".

2. **Verify paths**:
   - Phone path: `adb shell ls -la <phone_path> | head`. If not found, stop and report.
   - Local path: create if missing, but confirm with the user if the parent is outside `~`.

3. **Pull strategy** — default to incremental:
   - Get the newest file mtime already present locally: `find <local_path> -type f -printf '%T@\n' | sort -n | tail -1`.
   - List phone files newer than that: `adb shell find <phone_path> -type f -newermt @<epoch>`.
   - If the incremental set is empty, say so and stop.
   - For each newer file, `adb pull "<phone_file>" "<local_path>/"`. Preserve directory structure when the mapping is a tree.

4. **Summary**: report count of files pulled, total bytes, top 3 file types, and the destination path. Do not delete files from the phone unless the user explicitly asks.

## Modes

- **Incremental** (default): only files newer than the newest local file.
- **Full**: mirror the entire phone path. Warn about duplicates and size before starting.
- **Dry-run**: list what would be pulled without transferring. Offer this whenever the expected size is > 1 GB.
