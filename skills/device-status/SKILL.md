---
name: device-status
description: Report the status of Android devices connected via ADB — serial, manufacturer, model, Android version, battery, storage, and whether the device matches the onboarded profile in <workspace>/profile.yaml. Use when the user asks "is my phone connected", "what phone is this", or before any other adb-ops operation to confirm the right device is in scope.
---

# adb-ops: device-status

Quick health snapshot of the connected Android device(s).

## Procedure

1. Run `adb devices -l`. If no devices, tell the user to plug in the phone and authorise USB debugging.

2. For each device, collect:
   - Serial, model, manufacturer, Android version, build fingerprint
   - Battery: `adb -s <serial> shell dumpsys battery | grep -E 'level|status|temperature'`
   - Storage: `adb -s <serial> shell df -h /sdcard`
   - Uptime: `adb -s <serial> shell uptime`

3. Cross-reference with `<workspace>/profile.yaml` (if present). Flag mismatches (e.g. different serial than onboarded — "this is a different phone than the one you onboarded").

4. Report as a compact table. If multiple devices are connected, remind the user to pass `-s <serial>` to downstream skills.
