<div align="right">
  <a href="README_AR.md">
    <img src="https://img.shields.io/badge/🌍Read_in_Arabic-0A84FF?style=for-the-badge" alt="Read in Arabic" />
  </a>
</div>

# 🚀 AutoDeploy — Automated Windows Software Deployment & OS Setup Tool

**AutoDeploy** is a standalone, zero-dependency Windows software deployment and post-installation automation tool designed to run immediately on a **fresh Windows installation** (Bare-Metal OS). 

Instead of spending hours after formatting your PC manually clicking "Next" on dozens of installers, hunting for latest versions, copying license files, restoring `AppData` configurations, and recreating missing desktop shortcuts, **AutoDeploy** automates your entire post-format workflow in a single click—wrapped in a 60FPS **Liquid Glass** interface.

---

## ⚙️ How AutoDeploy Works

1. **Build Your Queues Once:** Drag and drop your installers (`.exe`, `.msi`), batch scripts (`.bat`, `.cmd`), registry tweaks (`.reg`), and PowerShell scripts (`.ps1`) into AutoDeploy.
2. **Portable `.txt` Persistence:** Your queues and custom post-install steps are saved automatically alongside the executable in two human-readable, portable files (`AutoDeploy Silent Queue.txt` and `AutoDeploy Custom Queue.txt`), making it 100% compatible with USB flash drives (`FAT32`/`exFAT`/`NTFS`) and external hard drives.
3. **One-Click Deployment After Formatting:** On a fresh Windows install, launch `AutoDeploy.exe` and click **Start Full Deployment Sequence**. AutoDeploy verifies what is already installed, checks for newer versions online, installs your programs, restores your backed-up settings, fixes desktop icons, and exports a timestamped execution report.

---

## ✨ Key Features & Architecture

### 1. Dual Deployment Queues: `1. Silent Setup` vs. `2. Custom Setup`
AutoDeploy divides your software into two sequential phases so you have full control over how each program is installed:

| Feature / Behavior | `1. Silent Setup` (Phase 1) | `2. Custom Setup` (Phase 2) |
| :--- | :--- | :--- |
| **Saved File** | `AutoDeploy Silent Queue.txt` | `AutoDeploy Custom Queue.txt` |
| **How It Runs** | Runs **100% unattended** in the background with zero pop-ups or wizard windows. | Opens the **normal interactive installer UI** so you can click through it manually. |
| **Best Used For** | Browsers, media players, archive tools, system runtimes (`vcredist`, `DirectX`, `.NET`), and `.bat`/`.reg` tweaks. | Heavy IDEs, compilers, or apps where you want to manually change the install drive (e.g., `D:\`) or pick custom checkboxes (e.g., `Git`, `Python`, `VS Code`, `PostgreSQL`). |
| **Queue Synchronization** | AutoDeploy detects the exact silent switch automatically and advances to the next item once finished. | AutoDeploy waits patiently until you finish and close the installer window before running its post-install steps and moving to the next program. |

> **💡 Smart Queue Move:** If you drag a program into `Custom Setup` that already exists in `Silent Setup` (or vice versa), AutoDeploy prompts you to move it seamlessly along with all of its configured **Additional Steps**.

---

### 2. Post-Install Engine (`⚙ Additional Steps`) — With Real Examples
Every program in either queue has its own **Additional Steps** manager. These steps execute **immediately after that specific program finishes installing**:

* **📋 Mode 1: Copy & Replace (Multi-File / Multi-Folder)**
  * *What it does:* Copies one or more files/folders to one or more destination directories while preserving original files.
  * *Real Example:* Automatically copying a license file (`revouninstallerpro5.lic`) into `C:\ProgramData\VS Revo Group\Revo Uninstaller Pro` right after Revo Uninstaller finishes installing.
* **✂ Mode 2: Move & Replace (Safe Multi-Destination Move)**
  * *What it does:* Copies selected files/folders to all target directories first, and only deletes the original source after verifying that every single copy succeeded.
  * *Real Example:* Moving downloaded plugin packs or theme folders into an application's installation directory and cleaning up the source folder afterward.
* **🚀 Mode 3: Run Program (Wait Until Closed)**
  * *What it does:* Launches a dependent `.exe`, `.bat`, `.reg`, or `.ps1` file right after the main installer finishes and pauses the deployment queue until you close that tool.
  * *Real Example:* Installing `Discord` silently, then automatically launching `VencordInstaller.exe` so you can apply your client mod, and waiting until you close Vencord before moving on to the next program in the queue.
* **💾 Mode 4: Backup from Current OS (Portable OS Config Snapshot)**
  * *What it does:* Before formatting your current Windows, click **`💾 Backup from Current OS`** to snapshot a program's settings/accounts folder (e.g., from `C:\Users\YourName\AppData\Roaming\...`) into `AutoDeploy_OS_Backups`. AutoDeploy automatically converts the restore destination into a dynamic Windows environment variable (`%APPDATA%`, `%LOCALAPPDATA%`, `%PROGRAMDATA%`, or `%USERPROFILE%`) and queues a restore step.
  * *Real Example:* Backing up your `qBittorrent`, `OBS Studio`, or `VS Code` configuration from your current Windows so that after a fresh Windows install—even if you choose a completely different Windows username—your settings, profiles, and extensions are restored automatically.

---

### 3. Universal Silent Switch Detector & Smart Retry
* **Dual-Encoding Binary Inspection (`ASCII` & `UTF-16LE`):** Automatically identifies installers built with `Inno Setup`, `NSIS`, `WiX Burn`, `Squirrel/Electron`, `Omaha/Chromium`, `InstallShield`, and `7-Zip SFX`.
* **Self-Help String Mining:** For custom C/C++ installers, AutoDeploy scans the binary's internal usage table to extract case-sensitive switches (such as `/SILENT` or `INSTALL_SILENT=1 /s`).
* **Smart Retry Fallback Chain:** If an unfamiliar installer rejects a silent switch (exiting with an error code in under `4.5 seconds`), AutoDeploy automatically retries universal fallback switches (`/s`, `/SILENT`, `/silent`, `/quiet /norestart`, `--silent`) until installation succeeds.

### 4. Online Auto-Updater (`winget`) & In-App Self-Updater
* **Pre-Install Program Updater:** Queries Microsoft's official `winget` repository before installing a local file. If a newer version exists online, it prompts you to download and replace the old installer, automatically **stripping outdated version numbers** from the local filename (e.g., `qBittorrent_v5.1.0.exe` ➔ `qBittorrent.exe`) and updating the `.txt` queue.
* **In-App GitHub Self-Updater:** Checks GitHub Releases in the background on startup. When a new version of AutoDeploy is published, an in-app glass dialog with a live **Progress Bar** downloads the new `.exe`, hot-swaps the running binary in-place (keeping your `.txt` queues linked), and restarts automatically.

### 5. Desktop Shortcut Sync & Timestamped Execution Logs
* **Start Menu ➔ Desktop Icon Sync:** Many silent installers skip creating desktop shortcuts. AutoDeploy snapshots `Start Menu\Programs` before and after each installation and automatically copies any newly created application shortcut to your Desktop.
* **Timestamped External Logs:** Exports a detailed `AutoDeploy_Log_YYYY-MM-DD_HH-MM-SS.txt` file alongside the program upon completion, recording the exact **Date**, **Start Time**, **End Time**, **Total Duration**, and every step's status.

---

## 🛠️ Real-World Challenges & How We Solved Them

During real bare-metal testing on fresh Windows installations, we encountered and engineered permanent solutions for 8 critical OS-level challenges:

| Challenge on Fresh Windows | Root Technical Cause | Engineered Solution in AutoDeploy |
| :--- | :--- | :--- |
| **1. Drag & Drop Blocked (`🚫` Cursor)** | Windows User Interface Privilege Isolation (**UIPI**) forbids dragging files from `explorer.exe` (standard user) into an elevated (`Administrator`) window. | AutoDeploy launches as a standard user so Drag & Drop works natively, requesting a single on-demand **UAC** elevation (`--autorun-install`) only when you click **Start Deployment**. |
| **2. Update Downloads Failing with `SSL: CERTIFICATE_VERIFY_FAILED`** | A fresh Windows OS has not yet downloaded Root Certificates from Windows Update, causing strict `HTTPS` handshakes in Python's `urllib` to fail. | Performs a single upfront internet check (skipping all online checks if offline to save time) and uses a **Smart SSL Fallback** that retries failed certificate handshakes using an unverified SSL context. |
| **3. False-Positive `[SKIPPED]` Detections on Fresh OS** | Scanning raw `Program Files` folder names matches generic vendor folders (`Microsoft`, `Windows`), and unanchored regex truncates words like `Uninstaller` into `Pro`. | Enforces strict word boundaries (`\b`) during metadata cleaning and matches normalized software names exclusively against Windows Uninstall Registry keys. |
| **4. Batch Files (`install_all.bat`) Failing with `Code 1` or Hanging on `pause`** | Folder paths containing spaces (e.g., `D:\Windows Programs`) break unquoted `%~dp0` calls inside batch scripts, and `pause` halts unattended execution waiting for a keypress. | Dynamically rewrites batch execution in a temporary runner to prepend `@cd /d "%~dp0"`, wraps unquoted `%~dp0` executable calls in quotes, strips `pause` commands, and redirects `stdin` to `DEVNULL`. |
| **5. Installers Rejecting `/S` (`Java`, `FastCopy`)** | Custom installers enforce case-sensitive switches (`/s` in Java or `/SILENT` in FastCopy) or store engine signatures in `UTF-16LE` rather than `ASCII`. | Combines dual-encoding binary signature scanning, internal help-string mining, and an execution timer that triggers the **Smart Retry** switch chain if an installer exits with an error in `< 4.5s`. |
| **6. Missing Desktop Icons After Silent Setup** | Most silent installers disable desktop shortcut creation by default unless explicitly instructed. | Appends `/MERGETASKS="desktopicon"` for Inno Setup and runs a **Start Menu-to-Desktop Sync** engine that diffs `Start Menu\Programs` before and after setup to copy new `.lnk` icons to the Desktop. |
| **7. Hidden System Folders (`C:\ProgramData`) Becoming Visible** | Creating directories or copying license/config files into system paths can alter parent folder attributes. | Snapshots parent directory flags via Win32 `GetFileAttributesW` prior to file operations and restores `FILE_ATTRIBUTE_HIDDEN` and `FILE_ATTRIBUTE_SYSTEM` via `SetFileAttributesW`. |
| **8. Executable Failing to Launch on Bare-Metal Windows** | `PyQt6` requires Visual C++ runtime libraries (`msvcp140.dll`, `vcruntime140.dll`) that may not exist on a fresh OS before redistributables are installed. | Bundles the required Visual C++ DLLs directly inside the standalone `AutoDeploy.exe` binary during compilation with `PyInstaller`. |

---

## 📄 License & Author
Developed by **Essam** — Copyright © 2026 Essam. All rights reserved.
