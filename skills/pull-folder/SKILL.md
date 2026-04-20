---
name: pull-folder
description: Pull an arbitrary folder (or single file) from the connected Android device to the local computer via `adb pull`. Unlike import-media this is ad-hoc — the user supplies the phone path and optional local destination. Use when the user wants "just grab /sdcard/<something>" that isn't in their saved mappings.
---

# adb-ops: pull-folder

Ad-hoc pull from the phone.

## Procedure

1. Resolve the phone path from the user's request. Verify it exists: `adb shell ls -la <path>`.

2. Resolve the local destination:
   - If user specified one, use it (create if missing).
   - Otherwise default to `~/Downloads/adb-pulls/<basename>-<timestamp>/`.

3. Estimate size first: `adb shell du -sh <path>`. If > 1 GB, show the size and confirm before proceeding.

4. Run `adb pull <phone_path> <local_path>`. Preserve directory structure.

5. Report file count, bytes transferred, destination path. Offer to save this as a new mapping in `~/.claude/adb-ops/mappings.yaml` if the user might repeat it.
