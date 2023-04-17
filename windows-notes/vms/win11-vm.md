# Windows 11 - VM

## First Install - Virtual Machine

### Change Windows 11 Theme

- Open **`regedit.exe`**

```powershell
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize
```

- Set to `0` the value of `AppsUseLightTheme` and `SystemUsesLightTheme`
- This will enable Dark Theme in Windows, without Windows activation.

[How to Change Themes in Window 11 Without Activating Windows](https://techwiser.com/change-theme-windows-11/)

- By using CMD commands:

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



### 

