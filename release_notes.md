# RapidOptimizer v1.1.7 — Release Notes

**Released:** June 27, 2026  
**Platform:** Windows 10 / 11 (64-bit) · Administrator required  
**Built with:** Go 1.22 · Wails v2.12 · Win32 API · Windows ETW

---

## What's New

### App Sleep Mode

A new dedicated page that lets you suspend (sleep) individual running processes to free up CPU and RAM — perfect for pausing heavy background apps while gaming.

- **Process list** — shows all running processes with name, PID, and current state (Running / Sleeping)
- **Sleep / Wake individual apps** — click to suspend a process instantly via `NtSuspendProcess`; wake it with `NtResumeProcess`
- **Advanced Sleeper Mode** — toggle to automatically re-suspend any app that restarts or spawns a new instance
- **Persistent state** — sleeping apps and advanced mode state are saved to `%AppData%\RapidOptimizer\sleeper.json`
- **Background watcher** — when Advanced Mode is on, a background goroutine polls every 3 seconds and re-suspends matching processes
- **Safe by design** — only the user can manually wake an app; no automatic wake timers

### Windows Tweaks

A new section at the bottom of the PC Optimize page with 23 toggle switches across 4 categories, each backed by Go methods that run PowerShell, registry, service control, and system utility commands.

- **System Tweaks (🔧):** Optimize Performance, Optimize Network, Disable Error Reporting, Disable Compatibility Assistant, Disable Print Service, Disable Fax Service, Disable Sticky Keys, Disable SmartScreen
- **Privacy & Networking (🔒):** Disable Telemetry Tasks, Disable Media Player Sharing, Disable HomeGroup, Disable SMBv1, Disable SMBv2 (⚠️ orange warning)
- **Disk & Storage (💾):** Disable System Restore, Disable Superfetch, Disable Hibernation, Disable NTFS Last Access Timestamp, Disable Search Indexing
- **Application Tweaks (🖥️):** Disable Office / Firefox / Chrome / NVIDIA / Visual Studio telemetry
- Each tweak stores its state in `localStorage` and calls the Go backend via Wails bindings
- Toggle ON calls `ApplyTweak(tweakID)`, toggle OFF calls `RevertTweak(tweakID)`
- SMBv2 toggle rendered in orange (#f59e0b) with ⚠️ warning icon
- All commands execute hidden with no console window

---

## Download

| File | Type | SHA-256 |
|------|------|---------|
| `RapidOptimizer_Setup_v1.1.7.exe` | Installer | *(run `Get-FileHash` to verify after download)* |
| `RapidOptimizer_v1.1.7.exe` | Portable | *(run `Get-FileHash` to verify after download)* |

Always verify the SHA-256 checksum before running. Download only from the official GitHub Releases page.

```powershell
# Verify installer
Get-FileHash "RapidOptimizer_Setup_v1.1.7.exe" -Algorithm SHA256 | Format-List

# Verify portable
Get-FileHash "RapidOptimizer_v1.1.7.exe" -Algorithm SHA256 | Format-List
```

---

**Requirements:** Windows 10/11 64-bit

---

## Upgrade Notes

- No database migrations or config file changes required
- Existing `overlay.json` and `license.key.dpapi` are preserved
- Sleeper state is stored in `sleeper.json` — no conflict with existing config
- Welcome screen only shows once per machine (localStorage key `ro_welcomed`)
- If upgrading from v1.1.6, re-sign the new binary with your code signing certificate before distribution

---

## Known Limitations

- PSU wattage cannot be read from hardware — user entry or formula estimate only
- GPU usage in the overlay is estimated from CPU load, not from a GPU sensor API
- ETW FPS counter requires Administrator privileges; falls back to estimated FPS without it
- Display brightness via DDC/CI requires the monitor's OSD DDC/CI setting to be enabled
- Software gamma brightness fallback does not work in Remote Desktop sessions
- Sleeper Mode cannot suspend system-critical processes (protected by Windows)
- Some tweaks (SMB, System Restore) require a restart to take full effect

---

## Full Changelog

See [CHANGELOG.md](./CHANGELOG.md) for the complete version history.

---

*© 2026 Chamika Samaraweera — [Team Infinity](https://teaminfinity.lk) · chamika@teaminfinity.lk*
