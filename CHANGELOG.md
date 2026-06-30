# Changelog

All notable changes to RapidOptimizer are documented here.  
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [1.1.8] — 2026-06-30

### Added

- **Hold-to-Confirm** — destructive buttons (cleaner page, reset network, uninstall app, delete startup) now require an ~800ms press-and-hold before executing
  - Gold fill overlay (`rgba(255,209,102,0.25)`) grows left-to-right inside the button via `::before` pseudo-element
  - Releasing early cancels and snaps the fill back to 0% in 150ms
  - Capture-phase click interceptor blocks all quick clicks on `[data-hold-confirm]` buttons
  - Supports mouse, touch, and keyboard (Enter/Space) events
  - MutationObserver handles dynamically created buttons (uninstall, delete startup)
  - Tooltip "Hold to confirm" added to all gated buttons

- **Per-Feature License Gating** — license checks are now per-action instead of all-or-nothing
  - `Clean()` is now completely free (no license required)
  - `Optimize()` uses an `advancedTweakActions` map — only System Tweak actions (Xbox Game Bar, HAGS, Nagle, Priority Boost, Timer Resolution, SSD, Search Indexing, Superfetch) require a license
  - Power plans, memory tools, and all network actions are now free
  - PRO badges (`<span class="pro-badge">PRO</span>`) on locked System Tweak cards
  - License gate no longer blocks startup — free users reach the dashboard immediately
  - Reactive gate: when a locked action returns "🔒 ...", the license page appears automatically

- **Sleeper Mode Redesign** — full UX/UI polish pass on the App Sleeper page
  - Unified process list: single table showing all processes with Running/Sleeping status badges
  - Checkbox column with Select All for multi-select bulk operations
  - "Sleep Selected" button sleeps multiple apps sequentially
  - Search box with clear (×) button and result count indicator ("12 of 34 processes")
  - Shimmer loading skeleton instead of plain text
  - Process rows show both `display_name` and `process_name` for better scannability
  - Generic file icon placeholder per row
  - Advanced Mode info tooltip explaining what it does
  - Sleeping count KPI card is now the most visually prominent stat (double width, blue tint, larger font)

### Changed

- License gating refactored from blanket `requireLicense("PC Optimize")` to per-action `advancedTweakActions` map in `app.go`
- `checkLicenseGate()` removed from auto-start init — free users no longer blocked on launch
- `showBanner()` now detects "🔒" responses and reactively shows the license gate
- `confirm()` dialogs removed from `uninstallApp()` and `deleteStartup()` — hold-to-confirm replaces them
- Sleeper page markup completely rewritten: two separate tables merged into one unified list
- `renderSleeperProcesses()` now merges running + sleeping processes into a single view
- `renderSleeperSleeping()` removed (functionality merged into unified renderer)

### Technical

- New CSS: `.pro-badge`, `.sleeper-status-badge`, `.sleeper-row-sleeping`, sleeper checkbox styles
- New JS: `escHtml()`, `clearSleeperSearch()`, `toggleSelectAllSleeper()`, `updateSleepSelectedBtn()`, `sleepSelectedApps()`
- `advancedTweakActions` map in `app.go` defines exactly 17 locked action strings
- MutationObserver in hold-to-confirm logic handles dynamically created buttons
- All existing function signatures preserved (`loadSleeperState`, `filterSleeperProcesses`, etc.)

---

## [1.1.7] — 2026-06-27

### Added

- **App Sleep Mode** — new dedicated page to suspend/resume individual running processes
  - Process list with name, PID, and current state (Running / Sleeping)
  - Sleep/Wake individual apps via `NtSuspendProcess` / `NtResumeProcess` Win32 API calls
  - Advanced Sleeper Mode toggle — automatically re-suspends apps that restart or spawn new instances
  - Persistent state saved to `%AppData%\RapidOptimizer\sleeper.json`
  - Background watcher goroutine polls every 3 seconds when Advanced Mode is active
  - Safe by design — only the user can manually wake an app

- **Windows Tweaks** — new section at the bottom of PC Optimize with 23 toggle switches across 4 categories
  - **System Tweaks (🔧):** Optimize Performance, Optimize Network, Disable Error Reporting, Disable Compatibility Assistant, Disable Print Service, Disable Fax Service, Disable Sticky Keys, Disable SmartScreen
  - **Privacy & Networking (🔒):** Disable Telemetry Tasks, Disable Media Player Sharing, Disable HomeGroup, Disable SMBv1, Disable SMBv2 (⚠️ orange warning)
  - **Disk & Storage (💾):** Disable System Restore, Disable Superfetch, Disable Hibernation, Disable NTFS Last Access Timestamp, Disable Search Indexing
  - **Application Tweaks (🖥️):** Disable Office / Firefox / Chrome / NVIDIA / Visual Studio telemetry
  - Each tweak stores state in `localStorage` and calls Go backend via Wails bindings
  - Toggle ON calls `ApplyTweak(tweakID)`, toggle OFF calls `RevertTweak(tweakID)`
  - SMBv2 toggle rendered in orange (#f59e0b) with ⚠️ warning icon
  - All commands execute hidden with no console window

### Changed

- PC Optimize page now includes Windows Tweaks section below existing System Tweaks
- Navigation handler loads Windows Tweak states on page visit

### Technical

- New file: `tweaks.go` — contains `ApplyTweak` and `RevertTweak` methods on `App` struct
- Uses `exec.Command` for PowerShell, `reg`, `sc`, `netsh`, `fsutil`, `powercfg` commands
- All commands run with `HideWindow: true` and `CreationFlags: 0x08000000`
- Wails bindings auto-generated for `ApplyTweak` and `RevertTweak`

## [1.1.6] — 2026-06-11

### Added

- **PSU Monitor** — new dedicated page with full power supply breakdown
  - Per-rail live estimation: +12V (CPU · GPU · fans · drives), +5V (SATA · USB), +3.3V (RAM · chipset)
  - Component draw breakdown: CPU watts, GPU watts, RAM watts, storage watts, system/fan overhead
  - Efficiency donut chart with color-coded load zones (green → amber → red above 85%)
  - Sparkline history charts for all three rails (60-point rolling window)
  - Hardware profile panel: CPU cores, CPU TDP, GPU TDP, RAM size, drive count, rated capacity
  - User-configurable PSU rated wattage input with quick presets (550W / 650W / 750W / 850W / 1000W)
  - Wattage persisted to `%AppData%\RapidOptimizer\psu_watts.txt`
  - Falls back to formula estimate when no wattage is entered (source labelled "estimated")

- **GPU TDP lookup table** — 100+ cards covered across:
  - NVIDIA RTX 40 series (4050–4090), RTX 30 series (3050–3090 Ti), RTX 20 series (2060–2080 Ti)
  - NVIDIA GTX 16 series (1650–1660 Ti), GTX 10 series (1030–1080 Ti)
  - AMD RX 7000 series (7500 XT–7900 XTX), RX 6000 series (6400–6950 XT), RX 5000 series
  - AMD RX 500 series, Intel Arc A-series (A310–A770)
  - Integrated graphics (Intel UHD, AMD Radeon Vega) — falls back to VRAM-based heuristic

- **CPU TDP estimation** — model-name matching for:
  - Intel Core i3/i5/i7/i9 (10th–14th gen), Core Ultra 7/9, Xeon, mobile variants
  - AMD Ryzen 3/5/7/9 (3000–9000 series), X3D variants, Threadripper, EPYC
  - Formula fallback based on core count and clock speed for unrecognised models

- **Startup loading screen** — full-window overlay shown immediately on app launch
  - Animated progress bar with realistic step messages (Initializing → Loading → Checking license → Ready)
  - Fades out smoothly once Wails runtime signals ready
  - Hard fallback: forcibly hides after 4 seconds if Wails takes too long

- **Tab-switch shimmer loader** — skeleton screen shown on every sidebar navigation click
  - Matches the layout of the destination page (header bar + KPI grid + card rows)
  - 320ms display window, hidden before content paints to avoid flash
  - Uses CSS `background-position` animation — no JavaScript repaint loop

- **First-run welcome screen** — 3-step onboarding modal for new users
  - Step 1: feature overview with pill badges
  - Step 2: Game Overlay HUD explanation
  - Step 3: license activation prompt with pricing summary
  - Dot-step indicator with animated active state
  - "Get Started" advances steps; final step navigates to the License tab
  - "Skip" dismisses immediately
  - Seen state stored in `localStorage` key `ro_welcomed` — never shown again after first close

### Changed

- PSU capacity source now labelled explicitly (`user` vs `estimated`) in `PSUStats` struct
- `GetPSUStats()` cache TTL is now 4 seconds (down from unbounded) to reflect live load changes
- `SetUserPSUWatts()` invalidates the PSU cache immediately so the next poll picks up the new value
- GPU TDP now uses dedicated lookup table instead of VRAM-only heuristic for all known cards

### Fixed

- PSU efficiency bar could exceed 100% on low-wattage PSU estimates — now capped at 100%
- `chipsetW` calculation no longer goes negative when RAM watts exceed the 3.3V rail total

---

## [1.1.5] — 2026-06-10

### Added

- Windows Installer via Inno Setup — Start Menu shortcut, desktop icon, Add/Remove Programs entry
- Portable EXE build — single file, no installation, USB-drive friendly
- SHA-256 checksums published alongside every release asset (`checksums.txt`)

### Changed

- Post-install launcher now uses `shellexec` to correctly trigger UAC elevation prompt
- Release pipeline automated via `gh release create` PowerShell script

---

## [1.1.2] — 2026-05-01  ·  Grand Release

### Added

- **In-Game HUD Overlay** — transparent always-on-top panel using `UpdateLayeredWindow` + chroma key transparency
- **ETW FPS Counter** — true frames-per-second via `Microsoft-Windows-DXGI` and `Microsoft-Windows-D3D9` event tracing
  - Zero GPU polling, zero hooks — purely kernel event counting
  - Atomic counter incremented on every `Present` call; 1-second ticker converts to FPS
- **Game auto-detection** — 50+ supported titles including CS2, Valorant, Fortnite, Apex, GTA V, Rust, PUBG, Tarkov
- **Overlay position control** — quick-corner presets, X/Y sliders, visual picker canvas, live preview panel
- **Display Control page** — DDC/CI brightness and contrast (WMI → DDC/CI → software gamma ramp cascade)
- **Refresh rate switching** — one-click 60 / 120 / 144 / 165 / 240Hz via `ChangeDisplaySettingsW`
- **Night Light toggle** — registry-based with fallback to opening Display Settings
- **Services Manager** — full start / stop / restart / disable control for all Windows services
- **Disk Health page** — S.M.A.R.T. status via `Get-PhysicalDisk`, volume usage table via `Get-Volume`
- **Startup Manager** — list, enable/disable, and delete startup entries via `StartupApproved` registry keys
- **PSU Monitor (initial)** — estimated system draw with basic CPU/GPU/RAM/storage breakdown

### Changed

- Metrics polling moved to shared background goroutine (`startMetricsPoller`) — UI and overlay read from cache only
- Deep Cleaner "Deep Clean All" now includes DNS flush and Windows Update cache in a single pass
- Overlay now runs as a separate child process (`--overlay` flag) with its own Win32 message loop

### Fixed

- Single-instance mutex now correctly skips the `--overlay` child process to prevent early exit
- `ensureAndActivateUltimate()` no longer creates duplicate plans when Ultimate Performance already exists

---

## [1.0.0] — 2026-Q1  ·  Initial Release

### Added

- **Live Dashboard** — real-time CPU, RAM, Disk, GPU, Network I/O updated every 2 seconds
- **CPU / RAM history charts** — 30-point rolling line charts via Chart.js
- **Gaming score** — composite 0–100 score based on CPU, RAM, and disk load
- **PC Optimizer** — feature toggles: Game Mode, HW Acceleration, Visual Effects, Startup Delay, Suggestions, Cortana, Telemetry, Defender, Edge
- **Power plan switcher** — Balanced, High Performance, Power Saver, Ultimate Performance
  - Smart duplicate detection: never creates a second Ultimate Performance plan
  - Falls back to High Performance + registry tweaks on Windows Home editions
- **Memory tools** — Clear RAM (working set trim + .NET GC), Aggressive RAM Free (`EmptyWorkingSet` on all processes)
- **Kill background apps** — terminates OneDrive, Spotify, Teams, Slack, Discord, Edge, Search Indexer
- **System tweaks** — HAGS, Nagle disable, foreground priority boost, SSD TRIM/last-access, Search Indexing, Superfetch toggle
- **Deep Cleaner** — User Temp, Windows Temp, Prefetch, Recycle Bin, Browser Cache (Chrome + Edge), Update Cache, Event Logs
- **Network Optimizer** — TCP/IP tuning, DNS flush, Winsock reset, Google/Cloudflare DNS one-click, IPv6 disable
- **App Manager** — searchable installed apps table with uninstall support (MSI + EXE, silent flags, post-removal verification)
- **License system** — activation, deactivation, expiry tracking via teaminfinity.lk license server
  - DPAPI-encrypted key storage (`%AppData%\RapidOptimizer\license.key.dpapi`)
  - License gate: redirects unlicensed users to activation screen
- **System tray** — minimize to tray, overlay toggle from tray menu, clean shutdown sequence
- **Single-instance guard** — Win32 named mutex, focuses existing window if already running
- **Frameless window** — custom title bar with minimize/close buttons, Wails drag region
- **Version injection** — build-time `AppVersion`, `AppCommit`, `AppDate` via `-ldflags`

---

*Built with Go 1.22 · Wails v2 · Vanilla JS · Win32 API · Windows ETW*  
*© 2026 Chamika Samaraweera — Team Infinity · chamika@teaminfinity.lk*
