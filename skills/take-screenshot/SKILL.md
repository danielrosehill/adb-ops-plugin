---
name: take-screenshot
description: Capture a screenshot from the connected Android device via ADB and pull it to the local computer. Defaults to saving under ~/Pictures/Phone/Screenshots/ with a timestamped filename, or uses the screenshots mapping from <workspace>/mappings.yaml if the user has one. Use when the user says "screenshot my phone", "grab a phone screenshot", or wants to capture the current phone screen.
---

# adb-ops: take-screenshot

Capture and pull a screenshot.

## Procedure

1. Verify a device is connected (`adb devices`). If multiple, require a serial or ask the user.

2. Determine the destination:
   - If `<workspace>/mappings.yaml` contains a mapping labelled `screenshots` (or phone_path `/sdcard/Pictures/Screenshots`), use its `local_path`.
   - Otherwise default to `~/Pictures/Phone/Screenshots/`. Create it if missing.

3. Capture directly to the host (avoids writing on-device):

   ```bash
   ts=$(date +%Y%m%d-%H%M%S)
   adb exec-out screencap -p > "<local_path>/phone-${ts}.png"
   ```

4. Verify the file is non-empty and is a valid PNG (`file "<path>"`). If it's 0 bytes, the device may be locked — tell the user.

5. Report the saved path and file size.

## Options

- Screen recording: if the user asks for a clip, use `adb shell screenrecord /sdcard/rec.mp4` in the background, prompt for stop, then `adb pull` and delete the on-device file. Default cap 3 minutes (ADB limit).
