# Windows DISM and SFC

---

## 🌐 Resources 🔗

> - [Using System File Checker in Windows - Microsoft Support](https://support.microsoft.com/en-us/windows/using-system-file-checker-in-windows-365e0031-36b1-6031-f804-8fd86e0ef4ca)
> - [DISM Image Management Command-Line Options | Microsoft Learn](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-image-management-command-line-options-s14?view=windows-11)
> - [Clean Up the WinSxS Folder | Microsoft Learn](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/clean-up-the-winsxs-folder?view=windows-11)

---

## Intro

Windows keeps many of its core system files, update payloads, and servicing metadata inside the **component store**, commonly associated with the `WinSxS` folder. When that store becomes inconsistent or corrupted, Windows Update may fail, `sfc /scannow` may be unable to repair files, and the system may show unexplained instability.

This guide walks through a safe repair workflow using two built-in Windows tools:

- **DISM** - repairs the Windows image and component store.
- **SFC** - verifies and repairs protected Windows system files.

> **Applies to:** Windows 10 and Windows 11 
>
> **Requirement:** Run all commands from an elevated Command Prompt, Windows Terminal, or PowerShell session.

---

## Quick repair sequence

Open **Command Prompt**, **PowerShell**, or **Windows Terminal** as Administrator, then run:

```cmd
DISM /Online /Cleanup-Image /CheckHealth
DISM /Online /Cleanup-Image /ScanHealth
DISM /Online /Cleanup-Image /RestoreHealth
sfc /scannow
```

After both tools complete, reboot Windows.

Then check whether the component store can be cleaned up:

```cmd
DISM /Online /Cleanup-Image /AnalyzeComponentStore
DISM /Online /Cleanup-Image /StartComponentCleanup
DISM /Online /Cleanup-Image /AnalyzeComponentStore
```

The final `AnalyzeComponentStore` run lets you compare the component store size before and after cleanup.

---

## What each command does

### `DISM /Online /Cleanup-Image /CheckHealth`

Performs a quick check to see whether Windows has already flagged the component store as corrupted. This command is fast because it does not perform a full scan.

### `DISM /Online /Cleanup-Image /ScanHealth`

Performs a deeper scan of the component store. This can take several minutes.

### `DISM /Online /Cleanup-Image /RestoreHealth`

Scans the Windows image for component store corruption and automatically attempts repairs. By default, DISM can use Windows Update as a repair source when required.

### `sfc /scannow`

Scans protected Windows system files and replaces corrupted files when possible. Run this **after** DISM so SFC can rely on a repaired component store.

### `DISM /Online /Cleanup-Image /AnalyzeComponentStore`

Reports component store size, reclaimable packages, cache data, and whether cleanup is recommended.

### `DISM /Online /Cleanup-Image /StartComponentCleanup`

Removes superseded components and reduces the size of the component store. This is useful after successful repairs or after installing many cumulative updates.

---

## Recommended step-by-step workflow

### 1. Update and reboot first

Before repairing system files, install pending Windows updates and restart if Windows asks you to. This avoids repairing an image while update operations are still pending.

### 2. Open an elevated terminal

Use one of these methods:

1. Press **Start**.
2. Search for **Command Prompt**, **PowerShell**, or **Windows Terminal**.
3. Right-click it and choose **Run as administrator**.

### 3. Check component store health

```cmd
DISM /Online /Cleanup-Image /CheckHealth
```

Use this as a quick first pass. If Windows reports that the component store is repairable, continue with the deeper scan and repair commands.

### 4. Run a deeper DISM scan

```cmd
DISM /Online /Cleanup-Image /ScanHealth
```

This checks for corruption more thoroughly. It may appear to pause at certain percentages; that is usually normal.

### 5. Repair the Windows image

```cmd
DISM /Online /Cleanup-Image /RestoreHealth
```

A successful result usually ends with:

```text
The restore operation completed successfully.
The operation completed successfully.
```

DISM logs details here:

```text
C:\Windows\Logs\DISM\dism.log
```

### 6. Run System File Checker

```cmd
sfc /scannow
```

Common results include:

- **Windows Resource Protection did not find any integrity violations.**
No protected system file corruption was found.

- **Windows Resource Protection found corrupt files and successfully repaired them.**
SFC repaired the detected files.

- **Windows Resource Protection found corrupt files but was unable to fix some of them.**
Review the CBS log and consider running DISM again or using a known-good repair source.

SFC logs details here:

```text
C:\Windows\Logs\CBS\CBS.log
```

To extract only SFC entries to your desktop:

```cmd
# cmd
findstr /c:"[SR]" %windir%\Logs\CBS\CBS.log > "%userprofile%\Desktop\sfcdetails.txt"

# Powershell
findstr /c:"[SR]" "$env:windir\Logs\CBS\CBS.log" > "$env:USERPROFILE\Desktop\sfcdetails.txt"
```

### 7. Reboot Windows

Restart the system after DISM and SFC complete. This helps Windows finish any pending servicing work and gives you a clean baseline for testing.

---

## Cleaning up the component store

The scheduled task located at `Task Scheduler Library\Microsoft\Windows\Servicing\StartComponentCleanup` automatically removes outdated and superseded Windows component-store files during maintenance to reduce WinSxS size and improve servicing efficiency.

After repairs are complete, check whether cleanup is recommended:

```cmd
DISM /Online /Cleanup-Image /AnalyzeComponentStore
```

If the report says:

```text
Component Store Cleanup Recommended : Yes
```

run it manually:

```cmd
DISM /Online /Cleanup-Image /StartComponentCleanup
```

Then run the analysis again:

```cmd
DISM /Online /Cleanup-Image /AnalyzeComponentStore
```

### Example result

```bash
dism /online /cleanup-image /AnalyzeComponentStore

Deployment Image Servicing and Management tool
Version: 10.0.26100.1150

Image Version: 10.0.26100.4351

[==========================100.0%==========================]

Component Store (WinSxS) information:

Windows Explorer Reported Size of Component Store : 16.52 GB

Actual Size of Component Store : 15.52 GB

    Shared with Windows : 7.58 GB
    Backups and Disabled Features : 7.67 GB
    Cache and Temporary Data : 267.32 MB

Date of Last Cleanup : 2025-06-12 12:24:28

Number of Reclaimable Packages : 19
Component Store Cleanup Recommended : Yes

The operation completed successfully.
```

In one real cleanup run, the component store changed from:

```text
Actual Size of Component Store : 15.52 GB
Backups and Disabled Features : 7.67 GB
Number of Reclaimable Packages : 19
```

to:

```text
Actual Size of Component Store : 11.01 GB
Backups and Disabled Features : 3.47 GB
Number of Reclaimable Packages : 2
```

That reclaimed roughly **4.51 GB** of component store space.

> ❗ Avoid manually deleting files from `C:\Windows\WinSxS`. Use DISM, Disk Cleanup, or Storage settings instead.

---

## Optional: use a local repair source

If `RestoreHealth` fails with an error such as `0x800f081f`, Windows may not be able to find the files needed for repair. In that case, mount a Windows ISO that matches your installed Windows version, edition, architecture, and language.

First, check which image indexes are available:

```cmd
DISM /Get-WimInfo /WimFile:D:\sources\install.wim
```

Then run DISM with a source path:

```cmd
DISM /Online /Cleanup-Image /RestoreHealth /Source:wim:D:\sources\install.wim:1 /LimitAccess
```

If your ISO uses `install.esd` instead of `install.wim`, use:

```cmd
DISM /Online /Cleanup-Image /RestoreHealth /Source:esd:D:\sources\install.esd:1 /LimitAccess
```

Replace `D:` with the drive letter of your mounted ISO, and replace `1` with the correct image index for your edition.

`/LimitAccess` prevents DISM from contacting Windows Update and forces it to use the specified source.

---

## Optional disk check

If you suspect file-system or disk problems, start with an online scan:

```cmd
chkdsk C: /scan
```

Only use the offline repair options when needed:

```cmd
chkdsk C: /f
```

`/f` may require a reboot on the system drive. Use `/r` only when you specifically need a surface scan for bad sectors, because it can take a long time on large drives.

---

## When to use this workflow

Use this repair sequence when you see symptoms such as:

- Windows Update repeatedly fails.
- `sfc /scannow` cannot complete or cannot repair files.
- Windows components, services, or built-in apps behave inconsistently.
- You see component store corruption in DISM output.
- You want to clean up superseded Windows components after updates.

This workflow is usually safer than jumping straight to an in-place upgrade, reset, or clean installation.

---

## What not to do

Do **not** manually delete the `WinSxS` folder or files inside it.

Do **not** run random cleanup scripts against component store folders.

Do **not** use `/ResetBase` unless you understand the trade-off. This can remove superseded component versions and prevent uninstalling existing Windows updates.

Do **not** interrupt DISM just because it appears paused at a percentage. Some stages can take several minutes.

---

## Maintenance routine

For normal systems, you do not need to run deep repair scans constantly. A practical routine is:

- Keep Windows Update current.
- Run `sfc /scannow` if you suspect system file corruption.
- Run `DISM /Online /Cleanup-Image /ScanHealth` when Windows Update, SFC, or system stability issues suggest component store problems.
- Run `DISM /Online /Cleanup-Image /AnalyzeComponentStore` after large update cycles if you want to check whether cleanup is recommended.

---

## Final command checklist

```cmd
# Run as Administrator

DISM /Online /Cleanup-Image /CheckHealth
DISM /Online /Cleanup-Image /ScanHealth
DISM /Online /Cleanup-Image /RestoreHealth
sfc /scannow

# Reboot Windows, then run:

DISM /Online /Cleanup-Image /AnalyzeComponentStore
DISM /Online /Cleanup-Image /StartComponentCleanup
DISM /Online /Cleanup-Image /AnalyzeComponentStore

# Optional disk check:
chkdsk C: /scan
```

---

