---
name: push-folder
description: Push a local folder or file to the connected Android device via `adb push`. Uses mappings from ~/.claude/adb-ops/mappings.yaml when the direction is `push` or `bidirectional`, or accepts ad-hoc source/destination from the user. Use when the user wants to "sync music to phone", "push ringtones", or any local-to-device transfer.
---

# adb-ops: push-folder

Ad-hoc or mapping-driven push from host to phone.

## Procedure

1. Resolve source (local) and destination (phone):
   - If the user names a mapping label with `direction: push|bidirectional`, use it.
   - Otherwise take explicit source/dest from the user.

2. Verify the local source exists and is readable.

3. Check free space on the phone: `adb shell df -h /sdcard`. Warn if the transfer would exceed 90% fill.

4. Run `adb push <local> <phone_path>`. For large directories, consider `adb push --sync` to skip unchanged files.

5. Report file count, bytes transferred, destination path. Do not delete the local source unless the user explicitly asks.

## Safety

- Never push into system paths (`/system/`, `/vendor/`, `/data/`) — those need root and are out of scope for this plugin.
- Default to `/sdcard/<destination>` if the user gives a relative phone path.
