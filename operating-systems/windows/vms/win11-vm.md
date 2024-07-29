# Windows 11 - VM

## First Install - Virtual Machine



## Basic Configuration

### Change Windows 11 Theme

- Open **`CMD ` as Administrator** and run:

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



### Set Language & Keyboard

```bash
powershell -Command "Set-WinUILanguageOverride -Language it-IT"
bcdedit /set {current} locale it-IT
powershell -Command "Set-WinUserLanguageList it-IT"
powershell -Command "Set-WinUILanguageFallback -Language en-US"
```



