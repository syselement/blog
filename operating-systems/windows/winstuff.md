# 📃 Everything Windows

![](.gitbook/assets/win2021-logo.png)



> 📜 In this 🪟 **Windows** notes repository, I store all of my notes related to the Windows operating system and Win apps.
>
> - 📌 *Some commands may be outdated.*

> ❗⚠️ This document contains powerful Windows commands - review each carefully before execution, as they may impact your system, and use them at your own risk!

---

## 🌐 Resources 🔗

> ### Install
>
> - [PERFORM CLEAN INSTALL OF WINDOWS 11 (SIX WAYS)](https://pureinfotech.com/clean-install-windows-11/)
> - [Rufus - Create bootable USB](https://rufus.ie/en/)
> - [Windows 11 ISO](https://www.microsoft.com/en-us/software-download/windows11)
> - [Windows Server Evaluate edition ISO](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022)
>
> ### Privacy
>
> - [How to protect your privacy in Windows 11](https://www.computerworld.com/article/3684413/how-to-protect-your-privacy-in-windows-11.html)
>
> - [Enable or Disable Choose Privacy Settings Experience at Sign-in in Windows 11 - ElevenForum](https://www.elevenforum.com/t/enable-or-disable-choose-privacy-settings-experience-at-sign-in-in-windows-11.12027/)
>
> - [Enable or Disable Location Services in Windows 11](https://www.elevenforum.com/t/enable-or-disable-location-services-in-windows-11.3003/)
> - [Enable or Disable Find My Device in Windows 11](https://www.elevenforum.com/t/enable-or-disable-find-my-device-in-windows-11.3861/)
> - [Change Diagnostic Data Settings in Windows 11](https://www.elevenforum.com/t/change-diagnostic-data-settings-in-windows-11.2621/)
> - [Enable or Disable Improve Inking and Typing in Windows 11](https://www.elevenforum.com/t/enable-or-disable-improve-inking-and-typing-in-windows-11.7575/)
> - [Enable or Disable Tailored Experiences in Windows 11](https://www.elevenforum.com/t/enable-or-disable-tailored-experiences-in-windows-11.7554/)
> - [Enable or Disable Advertising ID for Personalized Ads in Apps in Windows 11](https://www.elevenforum.com/t/enable-or-disable-advertising-id-for-personalized-ads-in-apps-in-windows-11.3730/)
> - [10 Settings You Should Change to Protect Your Privacy on Windows 11](https://beebom.com/change-windows-11-settings-protect-privacy/)
> - [Disable the Telemetry on Windows 11 and Stop Microsoft From Logging Your Data](https://wccftech.com/how-to/disable-the-telemetry-on-windows-11-and-stop-microsoft-from-logging-your-data/)
>
>
> ### Debloat Windows 11
>
> - [BloatyNosy - Builtbybel](https://github.com/builtbybel/BloatyNosy/)
> - [Windows11-Debloat-Privacy-Guide](https://github.com/TheWorldOfPC/Windows11-Debloat-Privacy-Guide)
> - [Win11Debloat - Raphire](https://github.com/Raphire/Win11Debloat)
> - [Win-Debloat-Tools - LeDragoX](https://github.com/LeDragoX/Win-Debloat-Tools)

---

## Install

### Windows 11 without Internet

> 📌 This can be done using the latest [Rufus](https://rufus.ie/en/) version to create a bootable USB drive with the preconfigured necessary bypasses and automatic local user creation on the bootable [Windows 11 ISO](https://www.microsoft.com/en-us/software-download/windows11).

> 🔗 [How to bypass internet connection to install Windows 11 - Pureinfotech](https://pureinfotech.com/bypass-internet-connection-install-windows-11/)

- Install Windows 11 via [USB ISO](https://pureinfotech.com/create-bootable-windows-11-usb-install-media/)
- At the first boot in OOBE (Out-of-the-box experience), select Region and Keyboard layout
- On the "Let's connect you to a network" screen, press `SHIFT+F10` on the keyboard to open the `Command Prompt`, type the following command and wait for the reboot.

```bash
OOBE\BYPASSNRO
```

- After the reboot, click `I don't have internet` when asked to connect and `Continue with limited setup`.
- Create a local default Windows 11 account and proceed.
  - Disable and answer `No` to all the Privacy Settings if not needed.

Proceed with the final [Configuration](#configuration).

------

## Configuration

> 📝 *Tip*: The following settings should be used with a [clean Windows install](https://pureinfotech.com/clean-install-windows-11/); otherwise, **proceed with caution** ⚠️.

### Privacy Settings

-TBA-

### Disable Bing Search

> 🔗 [How To Disable Bing Search On Windows 11](https://www.onmsft.com/how-to/how-to-disable-bing-search-on-windows-11/)
>

```powershell
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Search /v BingSearchEnabled /t REG_DWORD /d 0 /f
```

### Debloat Win

> 🔗 [BloatyNosy - by Builtbybel](https://github.com/builtbybel/BloatyNosy/)
>
> 🔗 [Windows11-Debloat-Privacy-Guide](https://github.com/TheWorldOfPC/Windows11-Debloat-Privacy-Guide)
>
> 🔗 [Win11Debloat - Raphire](https://github.com/Raphire/Win11Debloat)
>
> 🔗 [Win-Debloat-Tools - LeDragoX](https://github.com/LeDragoX/Win-Debloat-Tools)
>
> ❗ **The repositories above contain many useful and powerful scripts. Be sure to review them carefully before applying to your system, and do so at your own risk!** ⚠️

1. Download [https://github.com/Raphire/Win11Debloat](https://github.com/Raphire/Win11Debloat) repo and run `Run.bat` to configure
   - Run additional tweaks like `Regfiles\Hide_Onedrive_Folder.reg`
2. Download [https://github.com/LeDragoX/Win-Debloat-Tools](https://github.com/LeDragoX/Win-Debloat-Tools) repo and run other scripts if necessary (I prefer to run them manually)
   - Backup-System
   - Optimize-Privacy
   - Optimize-ServicesRunning
   - Optimize-TaskScheduler
   - Optimize-WindowsFeaturesList
   - Remove-BloatwareAppsList
   - Install-NerdFont
   - ...
   - and Software install tool

**Removing Telemetry and other unnecessary services**

> 📌 *The following commands may have already been executed by the scripts above. The command list may be outdated and requires some cleaning and fixing.*

- Open `cmd.exe` and type the following commands

```powershell
sc delete DiagTrack
sc delete dmwappushservice
sc delete WerSvc
sc delete OneSyncSvc
sc delete MessagingService
sc delete wercplsupport
sc delete PcaSvc
sc config wlidsvc start=demand
sc delete wisvc
sc delete RetailDemo
sc delete diagsvc
sc delete shpamsvc 
sc delete TermService
sc delete UmRdpService
sc delete SessionEnv
sc delete TroubleshootingSvc
for /f "tokens=1" %I in ('reg query "HKLM\SYSTEM\CurrentControlSet\Services" /k /f "wscsvc" ^| find /i "wscsvc"') do (reg delete %I /f)
for /f "tokens=1" %I in ('reg query "HKLM\SYSTEM\CurrentControlSet\Services" /k /f "OneSyncSvc" ^| find /i "OneSyncSvc"') do (reg delete %I /f)
for /f "tokens=1" %I in ('reg query "HKLM\SYSTEM\CurrentControlSet\Services" /k /f "MessagingService" ^| find /i "MessagingService"') do (reg delete %I /f)
for /f "tokens=1" %I in ('reg query "HKLM\SYSTEM\CurrentControlSet\Services" /k /f "PimIndexMaintenanceSvc" ^| find /i "PimIndexMaintenanceSvc"') do (reg delete %I /f)
for /f "tokens=1" %I in ('reg query "HKLM\SYSTEM\CurrentControlSet\Services" /k /f "UserDataSvc" ^| find /i "UserDataSvc"') do (reg delete %I /f)
for /f "tokens=1" %I in ('reg query "HKLM\SYSTEM\CurrentControlSet\Services" /k /f "UnistoreSvc" ^| find /i "UnistoreSvc"') do (reg delete %I /f)
for /f "tokens=1" %I in ('reg query "HKLM\SYSTEM\CurrentControlSet\Services" /k /f "BcastDVRUserService" ^| find /i "BcastDVRUserService"') do (reg delete %I /f)
for /f "tokens=1" %I in ('reg query "HKLM\SYSTEM\CurrentControlSet\Services" /k /f "Sgrmbroker" ^| find /i "Sgrmbroker"') do (reg delete %I /f)
sc delete diagnosticshub.standardcollector.service
reg add "HKEY_CURRENT_USER\SOFTWARE\Microsoft\Siuf\Rules" /v "NumberOfSIUFInPeriod" /t REG_DWORD /d 0 /f
reg delete "HKEY_CURRENT_USER\SOFTWARE\Microsoft\Siuf\Rules" /v "PeriodInNanoSeconds" /f
reg add "HKLM\SYSTEM\ControlSet001\Control\WMI\AutoLogger\AutoLogger-Diagtrack-Listener" /v Start /t REG_DWORD /d 0 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v AITEnable /t REG_DWORD /d 0 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v DisableInventory /t REG_DWORD /d 1 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v DisablePCA /t REG_DWORD /d 1 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v DisableUAR /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\MicrosoftEdge\PhishingFilter" /v "EnabledV9" /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v "EnableSmartScreen" /t REG_DWORD /d 0 /f
reg add "HKCU\Software\Microsoft\Internet Explorer\PhishingFilter" /v "EnabledV9" /t REG_DWORD /d 0 /f
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoRecentDocsHistory" /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\CompatTelRunner.exe" /v Debugger /t REG_SZ /d "%windir%\System32\taskkill.exe" /f
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\DeviceCensus.exe" /v Debugger /t REG_SZ /d "%windir%\System32\taskkill.exe" /f
```

**Scheduled tasks**

```powershell
del /F /Q "C:\Windows\System32\Tasks\Microsoft\Windows\SettingSync\*" 
schtasks /Change /TN "\Microsoft\Windows\AppID\SmartScreenSpecific" /disable
schtasks /Change /TN "\Microsoft\Windows\Application Experience\AitAgent" /disable
schtasks /Change /TN "\Microsoft\Windows\Application Experience\Microsoft Compatibility Appraiser" /disable
schtasks /Change /TN "\Microsoft\Windows\Application Experience\ProgramDataUpdater" /disable
schtasks /Change /TN "\Microsoft\Windows\Application Experience\StartupAppTask" /disable
schtasks /Change /TN "\Microsoft\Windows\ApplicationData\DsSvcCleanup" /disable
schtasks /Change /TN "\Microsoft\Windows\Autochk\Proxy" /disable
schtasks /Change /TN "\Microsoft\Windows\Clip\License Validation" /disable
schtasks /Change /TN "\Microsoft\Windows\CloudExperienceHost\CreateObjectTask" /disable
schtasks /Change /TN "\Microsoft\Windows\Customer Experience Improvement Program\BthSQM" /disable
schtasks /Change /TN "\Microsoft\Windows\Customer Experience Improvement Program\Consolidator" /disable
schtasks /Change /TN "\Microsoft\Windows\Customer Experience Improvement Program\KernelCeipTask" /disable
schtasks /Change /TN "\Microsoft\Windows\Customer Experience Improvement Program\Uploader" /disable
schtasks /Change /TN "\Microsoft\Windows\Customer Experience Improvement Program\UsbCeip" /disable
schtasks /Change /TN "\Microsoft\Windows\Diagnosis\RecommendedTroubleshootingScanner" /disable
schtasks /Change /TN "\Microsoft\Windows\Diagnosis\Scheduled" /disable
schtasks /Change /TN "\Microsoft\Windows\DiskDiagnostic\Microsoft-Windows-DiskDiagnosticDataCollector" /disable
schtasks /Change /TN "\Microsoft\Windows\DiskFootprint\Diagnostics" /disable
schtasks /Change /TN "\Microsoft\Windows\FileHistory\File History (maintenance mode)" /disable
schtasks /Change /TN "\Microsoft\Windows\License Manager\TempSignedLicenseExchange" /disable
schtasks /Change /TN "\Microsoft\Windows\Location\Notifications" /disable
schtasks /Change /TN "\Microsoft\Windows\Location\WindowsActionDialog" /disable
schtasks /Change /TN "\Microsoft\Windows\Maintenance\WinSAT" /disable
schtasks /Change /TN "\Microsoft\Windows\Maps\MapsToastTask"  /disable
schtasks /Change /TN "\Microsoft\Windows\Maps\MapsUpdateTask" /disable
schtasks /Change /TN "\Microsoft\Windows\NetTrace\GatherNetworkInfo" /disable
schtasks /Change /TN "\Microsoft\Windows\PI\Sqm-Tasks" /disable
schtasks /Change /TN "\Microsoft\Windows\Power Efficiency Diagnostics\AnalyzeSystem" /disable
schtasks /Change /TN "\Microsoft\Windows\Power Efficiency Diagnostics\AnalyzeSystem" /disable
schtasks /Change /TN "\Microsoft\Windows\PushToInstall\LoginCheck" /disable
schtasks /Change /TN "\Microsoft\Windows\PushToInstall\Registration" /disable
schtasks /Change /TN "\Microsoft\Windows\Retail Demo\CleanupOfflineContent" /disable
schtasks /Change /TN "\Microsoft\Windows\Shell\FamilySafetyMonitor" /disable
schtasks /Change /TN "\Microsoft\Windows\Shell\FamilySafetyRefreshTask" /disable
schtasks /Change /TN "\Microsoft\Windows\Shell\FamilySafetyUpload" /disable
schtasks /Change /TN "\Microsoft\Windows\Subscription\EnableLicenseAcquisition" /disable
schtasks /Change /TN "\Microsoft\Windows\Subscription\LicenseAcquisition" /disable
schtasks /Change /TN "\Microsoft\Windows\Windows Error Reporting\QueueReporting" /disable
```

### Italian Dev Keyboard - Tilde and Backtick

- Install Italian Dev keyboard - [https://github.com/linuxiamo/devs-kb-layout-ita](https://github.com/linuxiamo/devs-kb-layout-ita) - and setup in **Time & language - Language & region - Options - Keyboards**

------

## CMD commands

> ❗ Most of the following commands have to be run by opening **`CMD` as Administrator**.

### Change Windows Theme

> 🔗 [How to Change Themes in Window 11 Without Activating Windows](https://techwiser.com/change-theme-windows-11/)
>

**Dark theme**

```powershell
reg add HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize /v AppsUseLightTheme /t REG_DWORD /d 0 /f
reg add HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize /v SystemUsesLightTheme /t REG_DWORD /d 0 /f
```

**Light theme**

```powershell
reg add HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize /v AppsUseLightTheme /t REG_DWORD /d 1 /f
reg add HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Themes\Personalize /v SystemUsesLightTheme /t REG_DWORD /d 1 /f
```

### Winget Upgrade Script

- Create a `UpgradePackages.bat` with the following content and run it to upgrade installed packages

```powershell
@echo off
:: Check for admin rights
>nul 2>&1 "%SYSTEMROOT%\system32\cacls.exe" "%SYSTEMROOT%\system32\config\system"

:: If error flag set, we do not have admin rights, so prompt for them
if '%errorlevel%' NEQ '0' (
    echo Requesting administrative privileges...
    powershell.exe -Command "Start-Process '%~dpnx0' -Verb RunAs"
    exit /B
)

:: Set the execution policy to allow local scripts to run
powershell -NoProfile -Command "Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force"

:: Run the winget upgrade command and log the output live
powershell -NoProfile -Command "Start-Transcript -Path UpgradeLog.txt -Force; winget upgrade --all; Stop-Transcript"

:: Indicate completion
echo All packages have been upgraded. Press any key to exit...
pause
```

### cmd Cheatsheet

```powershell
# System
set
set /?
ver
systeminfo
chkdsk
driverquery
sfc /scannow
shutdown /r
shutdown /a

# Network
ipconfig /all
ping example.com
tracert example.com
nslookup example.com
netstat -abon
netstat -abon | findstr :3389

# Files and Disk
cd
dir
dir /a
dir /s
mkdir dirname
rmdir dirname
type
more
copy
copy *.md
move
del
erase

# Tasks and Processes
tasklist
tasklist /FI "imagename eq notepad.exe"
tasklist /FI "pid eq 1516"
taskkill /PID targetpid
```



------

## Powershell commands

### PS1 Scripts

```powershell
# 1. Run the script with Bypass mode (One-time execution)
powershell -ExecutionPolicy Bypass -File <SCRIPT.ps1>
# Shorter version
powershell -ep Bypass -f <SCRIPT.ps1>

# 2. Temporarily set execution policy for the current session
Set-ExecutionPolicy Bypass -Scope Process -Force
<SCRIPT.ps1>  # Now run the script

# 3. Unblock the script file (if marked as untrusted)
Unblock-File -Path <SCRIPT.ps1>
<SCRIPT.ps1>  # Try running again

# 4. Run the script without changing execution policy (using Invoke-Expression)
powershell -Command "Get-Content <SCRIPT.ps1> | Invoke-Expression"
# Alternative (more compact)
iex (Get-Content <SCRIPT.ps1> -Raw)

# 5. Change execution policy permanently (requires admin)
Set-ExecutionPolicy Unrestricted -Scope CurrentUser -Force  # Allows all scripts
Set-ExecutionPolicy RemoteSigned -Scope LocalMachine -Force  # Allows only signed remote scripts

# 6. Run with -NoProfile to ignore execution policies
powershell -NoProfile -ExecutionPolicy Bypass -File <SCRIPT.ps1>

# 7. Reset execution policy back to restricted (optional, for security)
Set-ExecutionPolicy Restricted -Scope CurrentUser -Force  # Blocks all scripts again
```



### [Winget](https://learn.microsoft.com/en-us/windows/package-manager/winget/upgrade)

```bash
# Upgrade all installed packages to the latest version if available
winget upgrade --all
```

### Powercat ###

```powershell
Set-ExecutionPolicy Unrestricted
IEX (New-Object System.Net.webclient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1')

powercat -l -p 9000 -v
powercat -h
```

### Directory and File Listing Without Extensions

```bash
Get-ChildItem -Recurse -Directory | ForEach-Object {
    Write-Output "Directory: $($_.FullName)"
    Get-ChildItem -Path $_.FullName | Where-Object { -not $_.PSIsContainer } | ForEach-Object {
        $_.BaseName
    }
    Write-Output ""
} | Out-File -FilePath "C:\path\to\output.txt"
```

### Powershell Cheatsheet

```powershell
### Syntax ###
# Cmdlet --> Verb-Noun
# Cmdlet -Property "pattern*"

Find-Module -Name "PowerShell*"
Install-Module -Name "PowerShellGet"

Get-Command
Get-Command -CommandType "Function"
Get-Command -Name "Remove*"

Get-Help Get-Date
Get-Help Get-Date -examples

Get-Alias
Get-Alias | findstr echo

Get-ChildItem
Get-ChildItem -r -Filter '*.txt'
Get-ChildItem -Path ".\test\"
Get-ChildItem | Sort-Object Length
Get-ChildItem | Where-Object -Property "Extension" -eq ".txt"
Get-ChildItem | Where-Object -Property "Name" -like "file*"
Get-ChildItem | Where-Object -Property Length -gt 100
Get-ChildItem | Select-Object Name,Length
Get-ChildItem | Sort-Object Length -Descending | Select-Object -First 1
Select-String -Path ".\file.txt" -Pattern "text" 

New-Item -Path ".\test\dir" -ItemType "Directory"
New-Item -Path ".\test\dir\file.txt" -ItemType "File"
Remove-Item -Path ".\test\dir\file.txt"
Remove-Item -Path  ".\test\dir"
Copy-Item -Path .\test\dir\file.txt -Destination .\test\dir\file2.txt

Get-Content file
Set-Location -Path ".\Documents"

Get-ComputerInfo
Get-LocalUser
Get-NetIPConfiguration
Get-NetIPAddress
Get-Process
Get-Service
Get-Service -DisplayName "*remote*"
Get-NetTCPConnection
Get-FileHash -Path .\file.txt

Get-Help Invoke-Command -examples
Invoke-Command -ComputerName HOSTNAME -ScriptBlock {Get-Service}
```









------

## Software

### [Sysinternals](https://learn.microsoft.com/en-us/sysinternals/)

> 🔗 Web: [https://live.sysinternals.com/](https://live.sysinternals.com/)
>

- Via Windows Explorer:

```powershell
\\live.sysinternals.com\tools
```

- **`e.g.`** Copy `tools` folder to `C:\Program Files\` and rename it to **`sysinternals`**

---

### [VirusTotal Uploader ContextMenu](https://github.com/SamuelTulach/VirusTotalUploader)

1. Download `vt_setup.msi` from [https://github.com/SamuelTulach/VirusTotalUploader/releases](https://github.com/SamuelTulach/VirusTotalUploader/releases)
2. Run `vt_setup.msi` and install **VirusTotalUploader**
3. Create an account and Get an **API key** from [VirusTotal.com](https://www.virustotal.com/)
4. Copy the **API key**
5. Run `VirusTotalUploader`, paste API key and select *`Direct file upload`*

![](.gitbook/assets/image-20230604005608397.png)

6. *EXTRA*: Change the *context menu handler* by opening `regedit.exe` and searching for `VirusTotalUploader`.
   - It should be in `HKEY_CLASSES_ROOT\*\shell`
   - Change the `MUIVerb` data value, **e.g.** *Upload to VirusTotal*.

![](.gitbook/assets/image-20230604010017190.png)

7. Now you can right-click any file an select "*Upload to VirusTotal*" to scan it using VirusTotal

![](.gitbook/assets/image-20230604010222697.png)

> | VirusTotal - Free public API | Limits                 |
> | :--------------------------- | ---------------------- |
> | Request rate                 | 4 lookups / min        |
> | Daily quota                  | 500 lookups / day      |
> | Monthly quota                | 15.5 K lookups / month |



------

## Troubleshooting

### Delete GRUB files from a Boot EFI partition in Windows

> 🔗 [How to delete GRUB files from a Boot EFI partition in Windows 10](https://linuxbsdos.com/2015/09/05/how-to-delete-grub-files-from-a-boot-efi-partition-in-windows-10/)

- Run a `cmd.exe` process with administrator privileges
- Run `diskpart`
- Type: `list disk` then `sel disk X` where X is the drive your boot files reside on
- Type `list vol` to see all partitions (volumes) on the disk (the EFI volume will be formatted in FAT, others will be NTFS)
- Select the EFI volume by typing: `sel vol Y` where Y is the SYSTEM volume (this is almost always the EFI partition)
- For convenience, assign a drive letter by typing: `assign letter=M:` where M is a free (unused) drive letter
- Type `exit` to leave disk part
- While still in the `cmd` prompt, type: `M:` and hit enter, where M was the drive letter you just created.
- Type `dir` to list directories on this mounted EFI partition
- If you are in the right place, you should see a directory called EFI
- Type `cd EFI` and then `dir` to list the child directories inside EFI
- Type `rmdir /S ubuntu` to delete the ubuntu boot directory

```powershell
## These commands are used to identify and select the hard drive

diskpart
list disk
sel disk 0

## These commands are used to list the partitions, select the Boot EFI partition, 
## then assign it a drive letter

list vol
sel vol 2
assign letter=D:
exit

## These commands are used to change into the Boot EFI folder and delete the GRUB folder

cd /d D:
ls
ls EFI
cd EFI
ls
rmdir /s ubuntu
```

---

