# 🔫 The Edge Annihilator 3000
###  v6.0*

## What Is This

This is a Windows batch file that removes Microsoft Edge from your computer.

Not just uninstalls it. **Removes** it. Hunts it. Tracks it across 13 phases of increasingly unhinged system operations, kills its processes, deletes its folders, lobotomises its registry keys, cancels its scheduled resurrection tasks, fires its background services, blocks its update servers in the hosts file, and then sits there, watching, every 10 seconds, in case it tries to come back.

Because it will try to come back.

It always tries to come back.

---

## Why Does This Exist

Microsoft Edge:
- Installs itself on every Windows 11 machine without asking
- Sets itself as the default browser without asking
- Opens itself when you click links in Outlook, Teams, or the Start Menu
- Runs background services at boot without asking
- Schedules tasks to reinstall itself if removed without asking
- Pins itself to your taskbar without asking
- Shows you Bing AI suggestions without asking
- Preloads itself at startup via "StartupBoost" — a feature whose entire purpose is to make Edge feel faster than it actually is — without asking
- Has a sidebar. With Copilot in it. That you did not ask for.
- Opens your PDFs. You did not ask for this either.

This script asks Edge **none** of those questions in return, and simply deletes it.

---

## Features

| Feature | Description |
|---|---|
| 🔄 **10-Second Polling Loop** | Checks for Edge constantly. It never stops watching. |
| 🚨 **Install Interception** | Catches EdgeUpdate mid-download and kills it before it finishes |
| 🧠 **Registry Lobotomy** | Wipes every HKLM and HKCU Edge key, file association, and capability grant |
| ⚰️ **Triple-Tap Process Kill** | taskkill → taskkill → taskkill → WMIC sweep. Three passes, no survivors |
| 🗂️ **Folder Genocide** | takeown + icacls + rmdir on every Edge folder across Program Files and SystemApps |
| 🧹 **AppData Purge** | Clears every user-level Edge cache, including the UWP package folder |
| 📅 **Scheduled Task Assassination** | Kills MicrosoftEdgeUpdateTaskMachineCore, UA, ShadowStackRollback, StartupBoostTask, and more |
| ⚙️ **Service Execution** | Stops, disables, and deletes edgeupdate, edgeupdatem, and MicrosoftEdgeElevationService |
| 🔗 **Shortcut Extermination** | Removes every desktop, Start Menu, taskbar, and Quick Launch Edge shortcut |
| 🏛️ **GPO Policy Lockout** | Writes Group Policy registry keys forbidding reinstall, StartupBoost, background mode, Bing sidebar, and Copilot |
| 📦 **Winget Uninstall** | Invokes Windows' own package manager to uninstall Edge, Beta, Dev, and WebView2 |
| 💿 **DISM Provisioned Removal** | Strips Edge as a baked-in Windows provisioned AppX package |
| 🏭 **WMIC Product Uninstall** | Unregisters Edge and EdgeUpdate as MSI-installed products |
| 🌐 **Hosts File Siege** | Blackholes Edge update and telemetry servers so even if Edge returns, it can't update or phone home |
| 😂 **Extremely Funny Logs** | Every phase narrated like a Deadpool voiceover. Non-repeating. Completely unhinged. |
| 🪟 **Auto UAC Elevation** | Double-click and click YES on the popup. That's it. No manual "Run As Administrator". |

---

## The 13 Phases

```
Phase 1  — Kill            Triple-tap all Edge processes + WMIC sweep
Phase 2  — Uninstall       Microsoft's own setup.exe used against itself (stable/msedge/dev/beta)
Phase 3  — Registry        Full HKLM + HKCU wipe, policy keys, file associations, startup entries
Phase 4  — Services        Stop + disable + delete all Edge background services
Phase 5  — Tasks           Delete all scheduled resurrection tasks
Phase 6  — Folders         takeown + icacls + rmdir on all Edge install directories
Phase 7  — AppData         Per-user cache, profile, and temp folder purge
Phase 8  — Shortcuts       Desktop, Start Menu, taskbar, Quick Launch shortcut removal
Phase 9  — Policies        GPO registry lockout (StartupBoost, Copilot, Bing, background mode, etc)
Phase 10 — Winget          Windows package manager uninstall (all channels + WebView2)
Phase 11 — DISM            Remove as provisioned AppX package, all users
Phase 12 — WMIC            MSI product uninstall via WMI
Phase 13 — Hosts Block     Blackhole Edge update/telemetry servers at the network level
```

Every cycle also runs **Install Interception** before the main detection, catching:
- `MicrosoftEdgeUpdate.exe` mid-run
- `setup.exe` running from an Edge directory
- `msedgewebview2.exe` attempting a quiet reinstall
- EdgeUpdate temp download folders staging a comeback
- The `MicrosoftEdgeAutoLaunch` run key re-appearing in the registry

---

## Usage

1. Download `EdgeAnnihilator3000.bat`
2. Double-click it
3. Click **Yes** on the UAC popup
4. Watch the logs
5. Leave it running in the background

It will run forever in a loop, checking every 10 seconds, intercepting every install attempt, and removing Edge every single time it dares to reappear.

---

## Requirements

- Windows 10 or Windows 11
- A grudge against Microsoft Edge
- Administrator privileges (handled automatically via UAC popup)
- Approximately 0 tolerance for Bing sidebars

---

## Compatibility Notes

| Tool | Notes |
|---|---|
| `taskkill` | Built into Windows, always available |
| `wmic` | Available on Win10/11. Deprecated in newer builds but still works |
| `winget` | Built into Windows 11 and updated Win10. Gracefully fails if missing |
| `dism` | Built into Windows, always available |
| `powershell` | Built into Windows, always available |
| `schtasks` | Built into Windows, always available |
| `sc` | Built into Windows, always available |

---

## FAQ

**Q: Will this break anything?**
A: It removes Edge and Edge-adjacent components. If you have apps that specifically require Edge's WebView2 runtime (some Electron apps, some Office features), those may stop working. Most people: fine.

**Q: Does this survive Windows Update?**
A: Windows Update may reinstall Edge. The Annihilator will detect and remove it again within 10 seconds of the next poll. The policy keys and hosts block slow the reinstall cycle significantly.

**Q: Edge keeps coming back. Why.**
A: Because Microsoft built Edge into Windows at a very deep level, and Windows Update can re-provision it. This is intentional and documented Microsoft behaviour. The Annihilator is aware. The Annihilator does not care. It will remove it again.

**Q: Is this legal?**
A: You are removing software from your own computer. Yes.

**Q: Why are the logs like this?**
A: Edge opened itself to show me a Bing AI tip while I was in the middle of something else. This is the outcome. I regret nothing.

**Q: Can I make the poll interval longer?**
A: Yes. Change `set "POLL_SECONDS=10"` near the top to whatever value you want. 10 seconds is the current setting because Edge has no concept of personal boundaries so neither does this script.

**Q: What's `8wekyb3d8bbwe`?**
A: Microsoft's App Store publisher ID for built-in Windows apps. Yes, that's the real name. Yes, a human being approved this. 

---

## Known Limitations

- On some Windows 11 builds, Edge is re-provisioned by Windows Update regardless of DISM removal. The script handles this by detecting and removing it again every cycle.
- The hosts file block covers known update endpoints as of 2025. Microsoft may add new endpoints. If Edge starts updating again, check `msedge.api.cdp.microsoft.com` and friends.
- WMIC is deprecated on Windows 11 24H2+. It still runs but may produce warnings. The other 12 phases cover the same ground.
- If you run this on a machine that uses Edge for kiosk/enterprise purposes: please don't. That's a different situation and this script will make it a bad day.

---

## Contributing

Found an Edge process this doesn't kill? A registry key it missed? An update endpoint that slipped through? A new Bing feature that appeared unprompted on your screen?

Open an issue. We will add it. We will add all of it.

---

## Disclaimer

This script modifies the Windows registry, deletes system-level files, alters the hosts file, and removes installed software. It is provided as-is for personal use. Run it on your own machine. Don't run it on machines you don't own. Don't be weird.

The author accepts no responsibility for broken WebView2 apps, missing Bing coupons, or the profound silence left behind when Edge is finally, truly gone.

It's actually quite peaceful.

---

## License

MIT. Do whatever you want. Just don't use it to install Edge on anyone's computer. That would be cruel.

---

*"Edge: the browser you didn't ask for, can't easily uninstall, and keeps reinstalling itself."*
*"This script: the answer to that."*
