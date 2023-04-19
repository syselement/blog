# 📃Windows Stuff

![](.gitbook/assets/win2021-logo.png)



> 📜 In this 🪟 **Windows** notes repository, I store all of my notes related to the Windows operating system and Win apps.

* 📌 _Some commands can be outdated._

## CMD commands

### Change Windows 11 Theme

- Open CMD AS ADMIN and run:

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

## Installations

### Basic Tools

### [Sysinternals](https://learn.microsoft.com/en-us/sysinternals/)

- Web: [https://live.sysinternals.com/](https://live.sysinternals.com/)
- Windows Explorer:

```powershell
\\live.sysinternals.com\tools
```

- Copy `tools` folder to `C:\Program Files` and rename it to **`sysinternals`**







------

