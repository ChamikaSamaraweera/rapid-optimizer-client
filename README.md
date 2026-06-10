<div align="center">

<img src="https://rapidoptimizer.teaminfinity.lk/ro_1.png" alt="RapidOptimizer Dashboard" width="800"/>

# RapidOptimizer 

**A complete Windows PC optimization suite built for gamers.**  
Clean your system, boost performance, monitor hardware in real-time, and overlay live stats directly in-game — all from one dark, purpose-built tool.

[![Latest Release](https://img.shields.io/github/v/release/ChamikaSamaraweera/rapid-optimizer-client?style=flat-square&color=00ff88&label=latest)](https://github.com/ChamikaSamaraweera/rapid-optimizer-client/releases/latest)
[![Platform](https://img.shields.io/badge/platform-Windows%2010%2F11-blue?style=flat-square)](https://github.com/ChamikaSamaraweera/rapid-optimizer-client/releases/latest)
[![License](https://img.shields.io/badge/license-Apache%202.0-orange?style=flat-square)](LICENSE)
[![Downloads](https://img.shields.io/github/downloads/ChamikaSamaraweera/rapid-optimizer-client/total?style=flat-square&color=purple)](https://github.com/ChamikaSamaraweera/rapid-optimizer-client/releases)

[🌐 Website](https://rapidoptimizer.teaminfinity.lk) · [📥 Download](https://github.com/ChamikaSamaraweera/rapid-optimizer-client/releases/latest) · [🐛 Report a Bug](https://github.com/ChamikaSamaraweera/rapid-optimizer-client/issues)

</div>

---

## 📦 Download

> **Source code is not public.** Only the compiled Windows executable is distributed via GitHub Releases.

| Version | File | SHA-256 Checksum |
|---------|------|-----------------|
| v1.1.5 | [RapidOptimizer_v1.1.5.exe](https://github.com/ChamikaSamaraweera/rapid-optimizer-client/releases/tag/v1.1.5) | `C7A4E923870CB00BF4A0F6CF9AB2C2AF9557FA755A583ECA97D3E620766B2BE5` |

### Verify your download (PowerShell)
```powershell
Get-FileHash "RapidOptimizer_v1.1.5.exe" -Algorithm SHA256 | Format-List
# Hash should match: C7A4E923870CB00BF4A0F6CF9AB2C2AF9557FA755A583ECA97D3E620766B2BE5
```

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| 📊 **Live Dashboard** | Real-time CPU, RAM, Disk, GPU, and Network I/O with animated history charts updated every 2 seconds |
| ⚡ **PC Optimizer** | Toggle Game Mode, Telemetry, Cortana, Visual Effects; switch power plans; kill background apps instantly |
| 🧹 **Deep Cleaner** | 8 targeted clean targets + Deep Clean All — temp files, prefetch, recycle bin, browser cache, update cache, event logs |
| 🌐 **Network Optimizer** | TCP/IP tuning, DNS flush, Winsock reset, one-click Google/Cloudflare DNS, IPv6 toggle |
| 🎮 **In-Game HUD Overlay** | Transparent always-on-top panel with live FPS (via ETW), ping, CPU/GPU/RAM usage, temperature, and network speed |
| 💾 **Disk Health** | S.M.A.R.T. monitoring with health status, volume usage, file system info, and instant alerts |
| 🚀 **Startup Manager** | View and enable/disable every startup entry to reduce boot time |
| ⚙️ **Services Manager** | Full Windows services control — start, stop, restart, or disable individual services |
| 🖥️ **Display Control** | DDC/CI monitor control — brightness, contrast, refresh rate (60–240Hz), night light toggle |

---

## 🎮 Auto-Detected Games (50+)

RapidOptimizer automatically activates the in-game overlay when it detects any of the following:

`CS2` `Valorant` `Fortnite` `Apex Legends` `GTA V` `Minecraft` `Cyberpunk 2077` `Warzone` `Rust` `PUBG` `Rainbow Six Siege` `Overwatch 2` `Elden Ring` `Destiny 2` `Escape from Tarkov` and many more...

---

## 🛠️ System Requirements

| Requirement | Detail |
|-------------|--------|
| **OS** | Windows 10 or Windows 11 (64-bit) |
| **Privileges** | Administrator (required for ETW, registry, power plans, services) |
| **Runtime** | No additional runtime required — single portable EXE |

---

## 🔐 Why Administrator Access?

RapidOptimizer requests elevation via an embedded manifest. Here's exactly what each privilege is used for:

- **ETW FPS Counter** — `StartTraceW` requires admin to trace DXGI/D3D9 present events
- **Power Plan Changes** — `powercfg` requires elevation
- **Registry Edits** — HKLM keys (Game Mode, Telemetry, Cortana) require admin
- **Defender Toggle** — `Set-MpPreference` requires admin
- **Service Control** — `sc config` requires elevation
- **Protected Folders** — Windows Temp and Prefetch are system-protected

---

## ⚙️ How It Works (Technical)

### Real FPS Counter
Uses Windows ETW (Event Tracing for Windows) to listen to `Microsoft-Windows-DXGI` and `D3D9` provider events. Every `Present` call from a game increments an atomic counter. A 1-second ticker converts this into a true FPS value — no GPU polling, no hooks, no estimates.

### Transparent Overlay
Runs as a separate child process with its own Win32 message loop. Uses `UpdateLayeredWindow` with a 32-bit DIB and `ULW_ALPHA` for per-pixel transparency. ~82% opaque panel, 100% transparent outside. Zero FPS impact on your game.

### Non-Blocking Metrics
A background goroutine polls CPU, RAM, and network I/O every second via `gopsutil` and caches results. Both the UI and overlay read from this shared cache — never from live syscalls — keeping the UI thread and game loop fast.

### Built With
`Go 1.22+` · `Wails v2` · `Vanilla JS` · `Win32 API` · `Windows ETW` · `gopsutil`

---

## 📄 License

Distributed under the [Apache 2.0 License](LICENSE).  
The source code is proprietary and not included in this repository. Only the compiled executable is made available.

---

## 👤 Author

**Chamika Samaraweera**  
📧 chamika@teaminfinity.lk  
🌐 [teaminfinity.lk](https://teaminfinity.lk)

---

<div align="center">
  <sub>Built with ❤️ by TeamInfinity · Sri Lanka</sub>
</div>
