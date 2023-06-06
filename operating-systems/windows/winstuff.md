# 📃 Everything Windows

![](.gitbook/assets/win2021-logo.png)



> 📜 In this 🪟 **Windows** notes repository, I store all of my notes related to the Windows operating system and Win apps.

* 📌 *Some commands can be outdated.*

------

## Usage and Configuration

### Privacy Settings

-TBA-

### Disable Bing Search

🔗 [How To Disable Bing Search On Windows 11](https://www.onmsft.com/how-to/how-to-disable-bing-search-on-windows-11/)

```bash
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Search /v BingSearchEnabled /t REG_DWORD /d 0 /f
```



------

## CMD commands

> ❗ Most of the following commands have to be run by opening **`CMD ` as Administrator**.

### Change Windows Theme

🔗 [How to Change Themes in Window 11 Without Activating Windows](https://techwiser.com/change-theme-windows-11/)

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





------

## Powershell commands

### Powercat ###

```powershell
Set-ExecutionPolicy Unrestricted
IEX (New-Object System.Net.webclient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1')

powercat -l -p 9000 -v
powercat -h
```





------

## Software

### [Sysinternals](https://learn.microsoft.com/en-us/sysinternals/)

🔗 Web: [https://live.sysinternals.com/](https://live.sysinternals.com/)

- Windows Explorer:

```powershell
\\live.sysinternals.com\tools
```

- **`e.g.`** Copy `tools` folder to `C:\Program Files\` and rename it to **`sysinternals`**

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

