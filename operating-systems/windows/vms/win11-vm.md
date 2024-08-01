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

---

### Set Language & Keyboard

```bash
powershell -Command "Set-WinUILanguageOverride -Language it-IT"
bcdedit /set {current} locale it-IT
powershell -Command "Set-WinUserLanguageList it-IT"
powershell -Command "Set-WinUILanguageFallback -Language en-US"
```

---

## VMware performance - Disable Memory Integrity

> ❗ I do not assume any responsibility for the potential risks or consequences associated with the disabled memory integrity.
>
> 🔗 [Options to optimize gaming performance in Windows 11 - Microsoft Support](https://support.microsoft.com/en-us/windows/options-to-optimize-gaming-performance-in-windows-11-a255f612-2949-4373-a566-ff6f3f474613)
>
> 🔗 [Enable or Disable Core Isolation and Memory Integrity in Windows 11](https://www.thewindowsclub.com/core-isolation-and-memory-integrity-in-windows-10)
>
> 🔗 [How to Disable or Remove Hyper-V in Windows 11](https://www.makeuseof.com/windows-11-disable-hyper-v/)
>
> - **This is done to ensure VMware Workstation virtualization operates smoothly without any performance issues.**
> - **Memory integrity** (hypervisor-protected code integrity) is a security feature of Core isolation that prevents attacks from inserting malicious code into high-security processes. Take your own risk by disabling it.

- Disable `Memory Integrity` - Windows Security -> Device Security -> Core Isolation details
- Open `cmd` as Administrator and run

```cmd
# Disables power throttling for the VMware virtual machine executable (64-bit version)
powercfg /powerthrottling disable /path "C:\Program Files (x86)\VMware\VMware Workstation\x64\vmware-vmx.exe"

# Disables power throttling for the main VMware Workstation executable
powercfg /powerthrottling disable /path "C:\Program Files (x86)\VMware\VMware Workstation\vmware.exe"

# Disables the Windows Hyper-V
bcdedit /set hypervisorlaunchtype off
```

**Enable or Disable Core Isolation and Memory Integrity using Registry**

```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\HypervisorEnforcedCodeIntegrity
```

1. Open `regedit`
2. Navigate to `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\`
3. Right-click on *Scenarios > New > Key*.
4. Name it as `HypervisorEnforcedCodeIntegrity`.
5. Create a New `DWORD (32-bit) Value`.
6. Name it as `Enabled`.
7. Set the Value data as `0` to disable.
8. Restart the computer.

---

