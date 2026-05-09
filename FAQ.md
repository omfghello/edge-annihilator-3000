# FAQ — The Edge Annihilator 3000

*Every question anyone has ever had or could have about a batch file that removes a browser.*

---

## General Questions

**Q: What is this?**  
A: A Windows batch file that removes Microsoft Edge from your computer and keeps it removed by running in a loop every 10 seconds. It has 12 removal phases, real-time install interception, network-level blocking, and extremely judgmental log messages. It is the most thorough approach to removing a pre-installed browser ever committed to a `.bat` file.

**Q: Why does this exist?**  
A: Because Microsoft Edge reinstalls itself after you delete it. This is not a bug. It is a documented, intentional, shipped feature of Windows. The Annihilator is the counter-feature.

**Q: Is this overkill?**  
A: Yes. That is the point. Edge is not a normal uninstall situation. Normal uninstallers do not need to block network endpoints, kill background services, nuke scheduled tasks that undo the uninstall, and run every 10 seconds in perpetuity. Edge required all of that. We delivered all of that.

**Q: Does it actually work?**  
A: Yes. Edge goes away. If it comes back, the loop catches it within 10 seconds and removes it again. If it downloads a fresh installer, the interception sweep deletes the installer before it runs. If it tries to phone home for a new copy, the hosts file blocks the call. Edge has multiple reinstall vectors. We blocked all of them.

**Q: How many phases does it have?**  
A: 12. Like the labours of Hercules, except Hercules got to fight interesting monsters. We are fighting a browser. It is arguably more annoying.

**Q: Why 12 phases? Isn't that excessive?**  
A: Edge exists in at least 12 distinct layers on a Windows machine: running processes, the official installer, the registry, background services, scheduled tasks, the install directory, user profile data, desktop shortcuts, group policy, the Windows package manager database, the AppX provisioning layer, and the MSI product database. Each phase targets one layer. If anything, 12 is efficient.

**Q: Who is this for?**  
A: Anyone who owns a Windows computer and would like it to not have Edge on it. That is the entire qualification. No technical knowledge required beyond being able to double-click a file and click Yes on a popup.

**Q: Is this safe?**  
A: It removes Edge and Edge-adjacent components. It does not touch anything unrelated to Edge. The source code is plain text, fully commented with jokes, and readable in about 20 minutes. You should read it before running it. You should read any script before running it as administrator. This is good practice that Edge ironically agrees with you on.

**Q: Can I read the source code?**  
A: It is a `.bat` file. Open it in Notepad. It is all there. No compiled code, no obfuscation, no network calls except the hosts file block written plainly in the source. What you see is what it does.

**Q: Will this void my warranty?**  
A: Removing a browser from a computer you own does not void a warranty. If your warranty somehow requires Edge to remain installed, you should speak to whoever sold you that computer and also possibly a lawyer.

---

## Installation and Setup

**Q: How do I run it?**  
A: Double-click `edge-annihilator-3000.bat`. Click Yes on the UAC popup. Watch the green text. That is the entire process.

**Q: Why does it show a UAC popup?**  
A: It needs administrator privileges to delete Edge's system-level files, modify the registry, stop services, and delete scheduled tasks. The UAC popup is Windows asking you to confirm this. Click Yes. If you click No, the script exits and Edge remains. This is the one scenario where we sympathize with Edge.

**Q: Why does the UAC popup show a VBScript file?**  
A: The script uses a tiny temporary VBScript file to trigger the UAC elevation. This is the most reliable elevation method for batch files, working correctly regardless of whether the script path has spaces in it. The VBScript file is deleted immediately after the popup fires. You can verify this in the source code on lines 11-17.

**Q: Do I need to install anything?**  
A: No. Everything it uses — `taskkill`, `sc`, `reg`, `schtasks`, `takeown`, `icacls`, `attrib`, `robocopy`, `wmic`, `dism`, `winget`, `powershell` — ships with Windows. `robocopy` has been included with every version of Windows since Vista. You already have all of it.

**Q: Can I run it from a USB drive?**  
A: Yes. The UAC elevation uses `%~s0` (the short path to the script) which works on any drive. Run it from wherever you want.

**Q: Can I put it in a startup folder so it runs automatically?**  
A: Yes. Add a shortcut to `edge-annihilator-3000.bat` in `%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup`. It will auto-launch at login. Note that it will trigger a UAC popup on each launch unless you set up a scheduled task with highest privileges, which is an advanced move but achievable via Task Scheduler with `Run with highest privileges` checked.

**Q: How do I stop it?**  
A: Close the console window. That is it. It does not install anything, does not write any persistence mechanism of its own (we are aware of the irony given what we are fighting), and does not run in the background. The hosts file changes it made will persist after closing, which is intentional.

**Q: How do I uninstall the Annihilator itself?**  
A: Delete the `.bat` file. That is the entire uninstall process. If you want to undo the hosts file changes, open `%SYSTEMROOT%\System32\drivers\etc\hosts` in Notepad as administrator and delete the lines marked `# Edge Annihilator 3000`. The script is a single file with no installer, no registry entries of its own, no services, and no scheduled tasks. We are not hypocrites.

---

## How It Works

**Q: What does Phase 1 actually do?**  
A: Runs `taskkill /F /T /IM` against nine Edge process names: `msedge.exe`, `msedgewebview2.exe`, `msedge_proxy.exe`, `MicrosoftEdgeUpdate.exe`, `MicrosoftEdgeSH.exe`, `MicrosoftEdge.exe`, `elevation_service.exe`, `msedgerecovery.exe`, and `msedge_pwa_launcher.exe`. Does this three times with 2-second gaps. Then runs two WMIC process sweeps as backup. If a process survives all five of those it has earned a philosophical discussion about persistence.

**Q: What is the robocopy mirror trick and why do we need it?**  
A: `rmdir /s /q` fails if any file in the directory tree is locked by another process. When it hits a locked file it stops and the entire directory survives. The robocopy mirror trick works differently: `robocopy /MIR` with an empty source folder makes the destination match the source. Since the source has nothing in it, the destination ends up with nothing in it. Robocopy handles locked files differently than rmdir and gets through situations rmdir cannot. The directory is then empty and `rmdir` succeeds. This is why v6.0 finally actually deletes everything when v5.0 missed some files.

**Q: What is `MicrosoftEdgeUpdateBrowserReplacement`?**  
A: A scheduled task that Microsoft ships with Windows whose purpose is to silently change your default browser back to Edge. They called it BrowserReplacement. The intent is in the name. It is not subtle. The Annihilator deletes it in Phase 5 and logs a dedicated note about how they named it BrowserReplacement specifically, not EdgeHelper or BrowserOptimizer, BrowserReplacement, as though daring someone to notice.

**Q: What does the hosts file block actually do?**  
A: The Windows hosts file is a local DNS override. Any domain listed there is resolved to the specified IP instead of going to the real DNS server. We route five Edge update domains to `0.0.0.0`, which is a non-routable address. Requests sent there go nowhere. Edge update servers receive no traffic. Edge cannot phone home to download a fresh copy of itself. It is a wall. A digital wall that took approximately 5 lines of batch to build.

**Q: What is `StartupBoost` and why is it disabled?**  
A: StartupBoost is a feature that preloads Edge into memory during Windows startup so that when you eventually open it, it appears to launch faster. It is not faster. It is borrowing time from your boot sequence and RAM budget to create the impression of speed. Disabling it means Edge does not get to cheat at its own benchmark. This is done via a policy registry key in Phase 9.

**Q: What is EdgeWebView2 and why does the Annihilator target it?**  
A: WebView2 is Edge repackaged as an embeddable browser component. Other applications can ship WebView2 to render web content inside their own interface. It is Edge in a trench coat calling itself a developer SDK. The Annihilator targets it because it is a vector for Edge to remain on the machine after the main Edge package is removed, and because any app using it is shipping an embedded copy of Edge without clearly labelling it as such.

**Q: What is `edgeupdatem` with an M at the end?**  
A: It is a variant of the EdgeUpdate service that runs on a per-minute schedule instead of the standard interval. The `m` stands for minute. There is a service on your machine updating Edge every minute. Or there was. Phase 4 takes care of it.

**Q: Why does Phase 3 remove webcam and microphone permissions?**  
A: Edge granted itself camera and microphone access through the Windows capability consent store. This is used for video calls and voice features in the browser. When Edge is uninstalled those permission grants should be cleaned up but often are not. Phase 3 removes them explicitly because there is no reason for a browser that no longer exists to retain camera access to a computer it is no longer installed on.

**Q: What is `MSEdgePDF`?**  
A: A file association that Edge installs for itself, making Edge the default handler for PDF files. You did not set this. Edge set it during installation. When you double-click a PDF, Edge opens. The Annihilator removes the `MSEdgePDF` registry key in Phase 3, restoring PDF handling to whatever you actually chose or to Windows' built-in reader.

**Q: Why does the script target `%SystemRoot%\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe`?**  
A: That path is where Edge lives when installed as a Universal Windows Platform app, which is how it ships on fresh Windows installs. The `_8wekyb3d8bbwe` suffix is the publisher ID Microsoft uses for their first-party apps. It was chosen by a human at Microsoft. We do not know which human. We have not ruled out that it was generated randomly and then accepted.

**Q: What does DISM do that the other phases do not?**  
A: DISM (Deployment Image Servicing and Management) can remove provisioned packages — packages that are staged to be installed for every new user account created on the machine. Even if you remove Edge for your current user, a provisioned Edge package means every new account starts with Edge already installed. DISM Phase 11 removes it from the provisioning stack so new accounts do not get Edge automatically.

**Q: What is the difference between `edgeupdate` and `MicrosoftEdgeElevationService`?**  
A: `edgeupdate` and `edgeupdatem` handle downloading and installing Edge updates. `MicrosoftEdgeElevationService` is a separate service that allows Edge's updater to perform privileged operations without requiring a UAC prompt. It is a pre-approved UAC bypass specifically for Edge updates. Phase 4 deletes all three.

---

## Edge Behaviour and Microsoft

**Q: Does Edge really reinstall itself?**  
A: Yes. Via a combination of the EdgeUpdate scheduled tasks, the EdgeUpdate service, the TaskCache registry keys, and the provisioned AppX package. Multiple independent mechanisms all aimed at the same outcome: Edge comes back. This is not an accident or an edge case. It is the intended product behaviour.

**Q: Why would Microsoft do this?**  
A: Browser market share. Edge's market share was approximately 3% when Microsoft rebuilt it on the Chromium engine (the same engine as Chrome). The aggressive reinstall behaviour, the default browser nagging, the BrowserReplacement task, the StartupBoost performance theater — all of it is an attempt to grow that number. It is effective in aggregate because most users do not know how to permanently remove Edge. Now one of them does.

**Q: Is Edge actually bad or is this script just dramatic?**  
A: Both. Edge is a functional browser that renders pages correctly and has reasonable performance. The script is extremely dramatic. However, the specific behaviours it targets — silent reinstall after deletion, scheduled tasks that change your default browser, services running constantly without consent, a nagging frequency that required a dedicated patch — those are documented and real. The drama is proportional.

**Q: Is it true Microsoft employees use Chrome?**  
A: Internal Microsoft surveys and various leaked documents have shown that a significant portion of Microsoft employees use Chrome as their primary browser. This information has been reported by multiple outlets. We cite it not to be cruel but because it is genuinely relevant context when the company is simultaneously shipping code to prevent you from removing their browser from your own machine.

**Q: Did Edge really nag users 14 times in a week?**  
A: Reports from 2021 documented Edge prompting users to change their default browser multiple times per day on some configurations. Microsoft acknowledged this and released updates to reduce the frequency. The prompts themselves were not removed. Their rate was throttled. This is a real thing that happened and then was partially fixed and then remained partially present.

**Q: What is the `BrowserReplacement` task actually doing at a technical level?**  
A: It monitors whether Edge is the default browser. If it detects that Edge is not the default, it can trigger a default browser reassignment process. The mechanism varies by Windows version but the effect is that even after you go through the Settings process to change your default browser to something else, this task can change it back without asking. The Annihilator's Phase 5 deletes it. Phase 9 also writes policy keys so that even if the task somehow survived, the policy prevents the reassignment from completing.

**Q: Is Edge based on Chrome?**  
A: Edge is built on Chromium, which is the open-source project that Chrome is also built on. Edge is not Chrome. It is a Microsoft-made browser that uses the Chromium rendering engine and JavaScript engine. This means it renders pages identically to Chrome and shares Chrome's extension ecosystem but is a distinct product with different features, services, and privacy implications. It is Chromium with a Bing dependency and Microsoft account integration and a shopping assistant and a sidebar and a Copilot button and a collections feature and a vertical tabs option and a startup boost and a startup page full of news from MSN.

**Q: Does Microsoft know people do things like this?**  
A: Yes. The existence of the `--force-uninstall` flag in Edge's own `setup.exe` suggests Microsoft anticipated and accommodated the desire to fully remove Edge. The flag works. We use it in Phase 2. It is the most poetic part of the script: Microsoft built an escape hatch into their own browser, and we found it.

**Q: What happened to Internet Explorer?**  
A: Microsoft retired Internet Explorer in June 2022. It is gone. We had nothing to do with it. We respect the retirement. It earned it after 27 years of rendering tables wrong.

---

## Compatibility and Edge Cases

**Q: Does this work on Windows 10?**  
A: Yes. All commands used are available on Windows 10. DISM, robocopy, winget (via Windows Package Manager which ships with Windows 10 1709 and later), and all registry paths are compatible.

**Q: Does this work on Windows 11?**  
A: Yes. Windows 11 made Edge harder to remove in several ways including changes to the default browser setting UI and additional AppX integration. The Annihilator targets Windows 11 specifically in Phases 11 and 12 which handle the AppX and MSI layers that Windows 11 uses more heavily.

**Q: Does this work on Windows 7 or 8?**  
A: Edge does not ship on Windows 7 or 8. If you somehow have Edge on Windows 7 or 8, you installed it voluntarily, which raises separate questions we are not prepared to answer.

**Q: Does this work on ARM Windows?**  
A: The core commands work on ARM. The ProgramFiles paths may differ for ARM-native vs x64 Edge installations. The script targets both `%ProgramFiles%` and `%ProgramFiles(x86)%` which covers most configurations but ARM-native Edge may install to a different path. If you are on ARM and Edge survives, check `%ProgramFiles%\WindowsApps` and file an issue.

**Q: What if I have Edge Beta, Dev, or Canary installed?**  
A: Phase 2 runs the uninstaller with both stable and dev channel flags. Phase 10 uninstalls Beta and Dev via winget. The Canary channel is not explicitly targeted because very few people install it and those who do are professionals who can sort it out themselves.

**Q: What about the Edge WebView2 Runtime installed by other apps?**  
A: The Annihilator targets it. If another application installed WebView2 as a dependency, that application may stop working. The Annihilator prioritises removing Edge-adjacent components over preserving applications that chose to depend on them. If a specific app breaks, it can usually be reinstalled and will pull WebView2 back as part of its own installation.

**Q: Will this affect Microsoft Teams?**  
A: Teams uses its own Chromium-based renderer and is not dependent on the system Edge installation. Teams should not be affected. If it is affected in your specific configuration, reinstalling Teams will resolve it.

**Q: Will this affect Microsoft Office?**  
A: Modern Office uses WebView2 in some versions for certain features like the Web Add-ins pane and some online content rendering. If WebView2 is removed and Office loses those features, reinstalling Office or downloading the WebView2 Runtime standalone installer from Microsoft will restore them.

**Q: Will this affect Windows Update?**  
A: No. Windows Update uses its own BITS-based download infrastructure, not Edge or WebView2. The hosts file block targets Edge update domains specifically and does not affect Windows Update domains.

**Q: What about the Microsoft Store?**  
A: The Microsoft Store is an AppX application that uses its own rendering stack. It is not dependent on Edge. Removing Edge does not break the Store. You can verify this by opening the Store after running the Annihilator. It will be fine. Probably still have Edge in it though.

**Q: Does this affect Edge-based features in Windows like the PDF viewer in Settings?**  
A: Some Windows 11 Settings panes use WebView2 to render web content. If WebView2 is removed these panes may show blank or error. This is Microsoft's choice to embed a browser into system settings pages and is the category of problem that the Annihilator considers to be Microsoft's problem.

**Q: What if Edge was installed by my company's IT department via Group Policy?**  
A: If Edge was pushed via Group Policy, Group Policy can push it back. The Annihilator's Phase 9 writes its own policy keys but machine-level GPO from a domain controller will generally take precedence over locally written keys. In an enterprise environment, the IT department controls the machine and if they want Edge installed they will keep it installed. This tool is for personal machines. If your IT department is inflicting Edge on you that is a workplace conversation.

**Q: What if I am an administrator on a domain-joined machine?**  
A: Local administrator rights on a domain-joined machine may not be sufficient to override domain Group Policy. Some Phase 3 registry operations and Phase 9 policy keys may be overwritten by the next Group Policy refresh cycle (typically every 90 minutes). Again: workplace conversation.

---

## What Breaks

**Q: What might stop working after running this?**  
A: Applications that use WebView2 as an embedded renderer. Common examples include some versions of Microsoft Office (Web Add-ins), some Electron apps that use Edge's WebView2 rather than Chromium directly, and some Windows 11 Settings panes. The Annihilator accepts these as acceptable casualties.

**Q: How do I know if something broke because of the Annihilator?**  
A: If an application that worked before now shows a blank white window, crashes on launch with a WebView2-related error, or explicitly says it cannot find the WebView2 runtime, the Annihilator caused it. The fix is reinstalling the WebView2 Runtime from Microsoft's website or reinstalling the affected application.

**Q: Can I reinstall WebView2 without bringing Edge back?**  
A: Yes. The WebView2 Runtime is available as a standalone download from Microsoft at `developer.microsoft.com/microsoft-edge/webview2`. Installing the runtime does not install the full Edge browser. The Annihilator will not target the standalone WebView2 Runtime on subsequent cycles because the runtime alone does not trigger the Edge detection logic (it does not create `msedge.exe` or the Edge Application folder). It will however be removed again if Phase 11 or 12 runs and finds it registered. This is a limitation we acknowledge.

**Q: What if my antivirus flags this?**  
A: Some antivirus software flags batch files that use `taskkill`, `reg delete`, `sc delete`, and `schtasks /delete` in combination, as these commands are also used by malware. The Annihilator uses all of them, on purpose, to remove a browser. If your antivirus flags it, you can whitelist the file or read the source and decide whether the operations it performs are ones you consent to. They are: killing Edge processes, deleting Edge registry keys, removing Edge services, removing Edge scheduled tasks, deleting Edge folders, and blocking Edge update endpoints. That is the complete list.

**Q: Can running this cause a Blue Screen of Death?**  
A: No. Deleting user-space files, registry keys, services, and scheduled tasks cannot cause a BSOD. The operations performed are all standard administrative actions. The most disruptive thing it does is modify the hosts file, which is a plain text file in System32. If the hosts file is corrupted somehow, Windows falls back to DNS normally. A corrupted hosts file is not a BSOD scenario.

---

## The Philosophical Section

**Q: Is removing Edge ethical?**  
A: You own the computer. You purchased it or it was given to you. The software on it is licensed to you for use, not imposed on you as a permanent resident. Removing software from your own computer is not an ethical question. It is property rights.

**Q: Am I violating any terms of service?**  
A: The Windows licence agreement does not require Edge to remain installed. Microsoft's terms do not prohibit removing their browser from your own computer. If they did, that would be a different and much larger conversation involving regulators in multiple jurisdictions who have already been having it.

**Q: Is this script anti-Microsoft?**  
A: No. It is specifically anti-the-behaviour-of-Edge-reinstalling-itself-and-replacing-your-default-browser-without-asking. Visual Studio Code is a Microsoft product and it is excellent. TypeScript is a Microsoft product and it is genuinely useful. GitHub is Microsoft-owned and broadly positive. The Annihilator has opinions about one specific browser and its specific installation behaviour. Microsoft is a large company and contains multitudes.

**Q: Should I feel bad for Edge?**  
A: Edge is a browser. It does not have feelings. Its development team worked hard on it and the browser itself is competent. The team that wrote the BrowserReplacement scheduled task also worked hard on it. We do not feel bad for the task.

**Q: What if Edge gets better?**  
A: If Microsoft stops the aggressive reinstall behaviour, removes the BrowserReplacement task, makes Edge genuinely removable through normal Windows uninstall, and stops the background services, we will note this in a future release. v7.0 would be a very short script. We would be at peace with that.

**Q: What does the Deadpool energy in the logs add to the user experience?**  
A: Removing a pre-installed browser from your own computer should not require a 615-line script running every 10 seconds in perpetuity. The fact that it does is absurd. The logs acknowledge the absurdity directly. Laughing at the situation is more productive than being furious at it. Also it makes the green text more entertaining to watch while you wait.

**Q: Is the Annihilator faster than Edge?**  
A: Yes. It starts up immediately, runs its operations in a few seconds per cycle, and does not preload itself into RAM at boot to give the appearance of speed. The Annihilator does not need to cheat. Edge does.

**Q: Will you ever make a version that targets Chrome?**  
A: We will make no further comment on Chrome at this time. One enemy at a time.

---

*Have a question not answered here? Open an issue. If it is a good question we will add it. If it is asking us to add Chrome to the target list we will close it politely.*
