# RapidOptimizer v1.1.8 — Release Notes

**Released:** June 30, 2026  
**Platform:** Windows 10 / 11 (64-bit) · Administrator required  
**Built with:** Go 1.22 · Wails v2.12 · Win32 API · Windows ETW

---

## What's New

### Hold-to-Confirm for Destructive Actions

Buttons that perform irreversible operations (cleaner actions, network reset, uninstall, delete startup) now require an ~800ms press-and-hold before executing. A gold fill overlay grows left-to-right inside the button while held; releasing early cancels and snaps the fill back instantly. Quick clicks are blocked entirely.

- Gold (`#ffd166`) semi-transparent overlay via `::before` pseudo-element
- 800ms linear fill transition, 150ms snap-back on cancel
- Works with mouse, touch, and keyboard (Enter/Space)
- Dynamically created buttons (uninstall, delete startup) handled automatically
- Tooltip "Hold to confirm" on all gated buttons

### Per-Feature License Gating

License checks are now granular instead of all-or-nothing:

- **Deep Cleaner** — completely free for all users (no license required)
- **Power Plans, Memory Tools, Network Optimizer** — all free
- **System Tweaks** (Xbox Game Bar, HAGS, Nagle, Priority Boost, Timer Resolution, SSD, Search Indexing, Superfetch) — remain PRO/locked
- PRO badges on locked System Tweak cards so free users can see what's gated
- Free users no longer blocked on startup — the license gate only appears reactively when a locked action is attempted

### Sleeper Mode Redesign

Full UX/UI polish pass on the App Sleeper page:

- **Unified process list** — single table showing all processes with Running/Sleeping status badges, instead of two separate tables
- **Bulk operations** — checkbox column with Select All, "Sleep Selected" button to suspend multiple apps at once
- **Search improvements** — clear (×) button, result count indicator ("12 of 34 processes")
- **Loading skeleton** — shimmer animation instead of plain text
- **Process identity** — each row shows both display name and process name with a file icon
- **Advanced Mode tooltip** — info icon explaining what the toggle does
- **Visual hierarchy** — sleeping count is now the most prominent stat on the page

---

## Download

| File | Type | SHA-256 |
|------|------|---------|
| `RapidOptimizer_Setup_v1.1.8.exe` | Installer | *(run `Get-FileHash` to verify after download)* |
| `RapidOptimizer_v1.1.8.exe` | Portable | *(run `Get-FileHash` to verify after download)* |

Always verify the SHA-256 checksum before running. Download only from the official GitHub Releases page.

```powershell
# Verify installer
Get-FileHash "RapidOptimizer_Setup_v1.1.8.exe" -Algorithm SHA256 | Format-List

# Verify portable
Get-FileHash "RapidOptimizer_v1.1.8.exe" -Algorithm SHA256 | Format-List
```

---

**Requirements:** Windows 10/11 64-bit

---

## Upgrade Notes

- No database migrations or config file changes required
- Existing `overlay.json`, `license.key.dpapi`, and `sleeper.json` are preserved
- Hold-to-confirm is opt-in per button via `data-hold-confirm="true"` — no existing behavior changes
- Free users will now see cleaner and network actions work without a license
- If upgrading from v1.1.7, re-sign the new binary with your code signing certificate before distribution

---

## Known Limitations

- PSU wattage cannot be read from hardware — user entry or formula estimate only
- GPU usage in the overlay is estimated from CPU load, not from a GPU sensor API
- ETW FPS counter requires Administrator privileges; falls back to estimated FPS without it
- Display brightness via DDC/CI requires the monitor's OSD DDC/CI setting to be enabled
- Software gamma brightness fallback does not work in Remote Desktop sessions
- Sleeper Mode cannot suspend system-critical processes (protected by Windows)
- Some tweaks (SMB, System Restore) require a restart to take full effect
- Sleeper process list does not include memory/CPU usage data (backend struct would need extension)

---

## Full Changelog

See [CHANGELOG.md](./CHANGELOG.md) for the complete version history.

---

*© 2026 Chamika Samaraweera — [Team Infinity](https://teaminfinity.lk) · chamika@teaminfinity.lk*
