# RapidOptimizer v1.1.6 — Release Notes

**Released:** June 11, 2026  
**Platform:** Windows 10 / 11 (64-bit) · Administrator required  
**Built with:** Go 1.22 · Wails v2.12 · Win32 API · Windows ETW

---

## What's New

### PSU Monitor

The new PSU Monitor page gives you a real-time estimated view of your power supply's load — broken down by rail, by component, and over time.

**Per-rail breakdown**

| Rail | Powers |
|------|--------|
| +12V | CPU, GPU, case fans, storage |
| +5V  | SATA drives, USB controllers |
| +3.3V | RAM, chipset, PCIe logic |

Each rail has its own live wattage readout, load bar (as % of your PSU's rated capacity), sub-component breakdown, and a 60-second sparkline history chart.

**Component draw**

The component panel shows individual estimated watts for CPU, GPU, RAM, storage, and motherboard/fans — plus a running total.

**Efficiency donut**

A donut chart shows your current PSU load percentage. The arc colour shifts from orange to red above 85% load to warn you before you approach the limits of your unit.

**Enter your PSU wattage**

Windows has no API to read PSU capacity. Enter your unit's rated watts from the label — there are quick-tap presets for 550W, 650W, 750W, 850W, and 1000W. The value is saved to disk and used for all load calculations. Without it, the monitor uses a formula estimate based on your CPU and GPU TDP.

**GPU TDP lookup table**

Over 100 cards are covered with accurate manufacturer TDP values: full RTX 40/30/20 series, GTX 16/10 series, RX 7000/6000/5000/500 series, and Intel Arc. Cards not in the table fall back to a VRAM-based heuristic.

**CPU TDP estimation**

Intel Core (10th–14th gen), Core Ultra, AMD Ryzen (3000–9000 series including X3D), Threadripper, EPYC, and mobile variants are all matched by model name. Unknown CPUs use a core-count and clock-speed formula.

> All values are software estimates — not hardware sensor readings. Actual draw depends on your specific silicon and workload. A Kill-a-Watt or similar inline power meter is the only way to get true wall-draw numbers.

---

### UI Loading Animations

Three new UI additions that make the app feel faster and more polished, all implemented as pure CSS/HTML/JS with no changes to the Go backend.

**Startup loader**  
A full-screen overlay with the RapidOptimizer logo and an animated progress bar appears immediately when the app launches. It steps through realistic messages (Initializing → Connecting to system → Reading hardware → Checking license → Ready) and fades out the moment the Wails runtime is ready. A 4-second hard fallback ensures it always clears even if the runtime is slow.

**Tab-switch shimmer**  
Clicking any sidebar item briefly shows a skeleton screen that matches the layout of the destination page — a title bar stub, a row of KPI card placeholders, and content card placeholders. It disappears after 320ms when the real content paints. The animation is a pure CSS `background-position` slide with no JavaScript loop.

**First-run welcome screen**  
New users see a 3-step modal on first launch. Step 1 introduces the app's features, step 2 explains the Game Overlay HUD, and step 3 prompts for license activation with pricing. Each step shows relevant feature pills and a dot indicator. After closing or completing the flow, the screen never appears again — the seen state is stored in `localStorage`.

---

## Download

| File | Type | SHA-256 |
|------|------|---------|
| `RapidOptimizer_Setup_v1.1.6.exe` | Installer | *(run `Get-FileHash` to verify after download)* |
| `RapidOptimizer_v1.1.6.exe` | Portable | *(run `Get-FileHash` to verify after download)* |

Always verify the SHA-256 checksum before running. Download only from the official GitHub Releases page.

```powershell
# Verify installer
Get-FileHash "RapidOptimizer_Setup_v1.1.6.exe" -Algorithm SHA256 | Format-List

# Verify portable
Get-FileHash "RapidOptimizer_v1.1.6.exe" -Algorithm SHA256 | Format-List
```

---


**Requirements:** Windows 10/11 64-bit

---

## Upgrade Notes

- No database migrations or config file changes required
- Existing `overlay.json` and `license.key.dpapi` are preserved
- PSU wattage is stored separately in `psu_watts.txt` — no conflict with existing config
- Welcome screen only shows once per machine (localStorage key `ro_welcomed`)
- If upgrading from v1.1.5, re-sign the new binary with your code signing certificate before distribution

---

## Known Limitations

- PSU wattage cannot be read from hardware — user entry or formula estimate only
- GPU usage in the overlay is estimated from CPU load, not from a GPU sensor API
- ETW FPS counter requires Administrator privileges; falls back to estimated FPS without it
- Display brightness via DDC/CI requires the monitor's OSD DDC/CI setting to be enabled
- Software gamma brightness fallback does not work in Remote Desktop sessions

---

## Full Changelog

See [CHANGELOG.md](./CHANGELOG.md) for the complete version history.

---

*© 2026 Chamika Samaraweera — [Team Infinity](https://teaminfinity.lk) · chamika@teaminfinity.lk*