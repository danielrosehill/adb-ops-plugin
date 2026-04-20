---
name: remove-bloatware
description: Remove or disable Android bloatware (carrier apps, OEM pre-installed apps) via ADB using `pm uninstall --user 0` or `pm disable-user`. Logs every operation to ~/.claude/adb-ops/bloatware-log.jsonl so the history is auditable and reversible. Use when the user wants to "remove bloatware", "uninstall carrier apps", "disable pre-installed apps", or "debloat my phone".
---

# adb-ops: remove-bloatware

Remove or disable unwanted packages on the device.

## Safety rules

- **Never uninstall** packages in this suspect-list without explicit user confirmation, even if the user says "remove all bloat": anything with `com.android.`, `com.google.android.gms`, `com.google.android.gsf`, `com.qualcomm.`, `com.mediatek.`, kernel/telephony/system UI packages. Breaking these can brick the device.
- Prefer `pm disable-user` over `pm uninstall --user 0` when unsure — it's reversible with `pm enable` and doesn't require a factory reset to restore.
- Always operate per-user (`--user 0`). Never run as system-wide uninstall without root.
- Confirm the device serial explicitly when multiple devices are connected.

## Procedure

1. **List candidates**:

   ```bash
   adb shell pm list packages -3               # third-party / user-installed
   adb shell pm list packages -s               # system packages (debloat usually here)
   adb shell pm list packages -d               # already disabled
   ```

   Offer a categorised view. If the user names a vendor ("Samsung bloat", "Xiaomi apps"), filter by common prefixes (`com.samsung.`, `com.miui.`, `com.xiaomi.`).

2. **Propose a shortlist** with one-line descriptions (look up unfamiliar packages — ask the user if unknown). Require explicit per-package or batch confirmation before acting.

3. **Act** on each confirmed package:

   ```bash
   # Preferred: reversible
   adb shell pm disable-user --user 0 <package>
   # Or permanent for this user:
   adb shell pm uninstall --user 0 <package>
   ```

4. **Log** every attempt (success or failure) by appending one JSON line to `~/.claude/adb-ops/bloatware-log.jsonl`:

   ```json
   {"ts":"<ISO-8601>","device":"<serial>","action":"disable|uninstall","package":"<pkg>","user":0,"method":"<cmd>","result":"success|failure","error":"<stderr if any>","notes":"<free text>"}
   ```

   Append only — never rewrite the file.

5. **Summary**: table of package / action / result. Remind the user how to restore: `adb shell pm enable <pkg>` (for disable-user) or `adb shell cmd package install-existing <pkg>` (for --user 0 uninstalls).

## Restore mode

If the user asks to "undo", "restore", or "bring back" a removed package, read `bloatware-log.jsonl`, filter to that package's most recent successful action, and run the inverse command. Append a new log entry with action `restore` / `enable`.
