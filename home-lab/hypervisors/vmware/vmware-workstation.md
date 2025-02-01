# VMware Workstation Pro

![vmware.com](.gitbook/assets/vmware_workstation.png)

> **VMware Workstation** is a hosted (Type 2) hypervisor that runs on x64 versions of Windows and Linux operating systems. It enables users to set up and manage virtual machines (VMs) on a single physical machine, allowing for the execution of multiple operating systems simultaneously.

---

## 🌐 Resources 🔗

> - [VMware Workstation Pro 17.0 - Docs](https://techdocs.broadcom.com/us/en/vmware-cis/desktop-hypervisors/workstation-pro/17-0.html)
> - [VMware Workstation Pro Download - Broadcom](https://support.broadcom.com/group/ecx/downloads) (free, no license required)
> - [VMware Workstation Pro - Chocolatey Software](https://community.chocolatey.org/packages/vmwareworkstation)

---

## Install and Windows tweaks

> ❗ I do not assume any responsibility for the potential risks or consequences associated with the disabled memory integrity and Virtualization-based security.
>
> 🔗 [Options to optimize gaming performance in Windows 11 - Microsoft Support](https://support.microsoft.com/en-us/windows/options-to-optimize-gaming-performance-in-windows-11-a255f612-2949-4373-a566-ff6f3f474613)
>
> 🔗 [Enable or Disable Core Isolation and Memory Integrity in Windows 11](https://www.thewindowsclub.com/core-isolation-and-memory-integrity-in-windows-10)
>
> 🔗 [How to Disable or Remove Hyper-V in Windows 11](https://www.makeuseof.com/windows-11-disable-hyper-v/)
>
> 🔗 [Download Device Guard and Credential Guard hardware readiness tool](https://www.microsoft.com/en-us/download/details.aspx?id=53337)
>
> 🔗 [Disabling Hyper-V hypervisor on Windows 11 Pro host (to get VMWare 17's CPL0 vs. ULM monitor mode)](https://community.broadcom.com/vmware-cloud-foundation/discussion/disabling-hyper-v-hypervisor-on-windows-11-pro-host-to-get-vmware-17s-cpl0-vs-ulm-monitor-mode) - 📌 The following steps include some key points from this highly informative article, which provides an excellent explanation of the issue.
>
> ### Nesting considerations
>
> **The following is done to ensure VMware Workstation virtualization operates smoothly without any performance issues, using its own VMware virtualization engine, instead of the slower Hyper-V API, to create the virtualized environment.**
>
> - **CPL0 (Current Privilege Level 0):** The VMM (Virtual Machine Monitor) runs directly on the host hardware using Intel VT-x or AMD-V, providing full control and optimal performance, with no nested layers.
> - **ULM (User-Level Monitor):** The VMM runs on top of Hyper-V, allowing coexistence with VMware Workstation but with added overhead and reduced performance, with an extra level of nesting.
>   - these values can be seen by searching `Monitor Mode:` in the `vmware.log` file of a running VM.
>
> - VMware and Hyper-V can coexist using the newer VMware version and `Windows Hypervisor Platform (WHP)`, but nested virtualization can be slower than normal. Your choice.
> - **Memory integrity** (hypervisor-protected code integrity) is a security feature of Core isolation that prevents attacks from inserting malicious code into high-security processes. Take your own risk by disabling it.

1. Run the command from an elevated Powershell to **DISABLE Hyper-V, all its subfeatures, Windows Hypervisor Platform, Virtual Machine Platform, and Windows Sandbox**:

```powershell
# Disables the Windows Hyper-V
bcdedit /set hypervisorlaunchtype off

Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux,VirtualMachinePlatform,Microsoft-Hyper-V-All -NoRestart

Get-AppxPackage -AllUsers *WindowsSubsystemForLinux* | Remove-AppxPackage -AllUsers
```

```powershell
# Check the state of the features, should be State: Disabled
Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
Get-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All
```

2. Run the command from an elevated Powershell to **DISABLE Memory Integrity and Virtualization-based security** (Device/Credential Guard) via Registry:

```powershell
reg add "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\HypervisorEnforcedCodeIntegrity" /v "Enabled" /t REG_DWORD /d 0 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\SystemGuard" /v "Enabled" /t REG_DWORD /d 0 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard" /v "EnableVirtualizationBasedSecurity" /t REG_DWORD /d 0 /f

reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DeviceGuard" /v "EnableVirtualizationBasedSecurity" /t REG_DWORD /d 0 /f
```

```powershell
# Check the state of the reg keys
$keys = @(
    "HKLM:\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\HypervisorEnforcedCodeIntegrity",
    "HKLM:\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\SystemGuard",
    "HKLM:\SYSTEM\CurrentControlSet\Control\DeviceGuard",
    "HKLM:\SOFTWARE\Policies\Microsoft\Windows\DeviceGuard"
)

$values = "Enabled", "EnableVirtualizationBasedSecurity"

foreach ($key in $keys) {
    if (Test-Path $key) {  # Check if the registry key exists
        foreach ($value in $values) {
            $result = Get-ItemProperty -Path $key -Name $value -ErrorAction SilentlyContinue
            if ($result -and ($result.PSObject.Properties.Name -contains $value)) {
                Write-Output "$key\$value : $($result.$value)"
            }
        }
    }
}
```

In case this is still not working, use the [Device Guard and Credential Guard hardware readiness tool](https://www.microsoft.com/en-us/download/details.aspx?id=53337) with the `-Disable` command.

3. ❗ **Reboot PC**

4. Install **VMware Workstation Pro**
   - use the downloaded `exe` file - [https://support.broadcom.com/group/ecx/downloads](https://support.broadcom.com/group/ecx/downloads) (login necessary)
     - **Stop and avoid** installing `Windows Hypervisor Platform (WHP)` (feature) when asked by the VMware Workstation installer - this means Hyper-V has not been fully disabled, try again the entire disabling process.
   - or install [latest VMware Workstation Pro package](https://community.chocolatey.org/packages/vmwareworkstation) via Chocolatey

```powershell
# Chocolatey install + VMware Workstation

Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

choco install vmwareworkstation -y
```

5. Disable power throttling for the VMware executables

```powershell
# Disables power throttling for the VMware virtual machine executable (64-bit version)
powercfg /powerthrottling disable /path "C:\Program Files (x86)\VMware\VMware Workstation\x64\vmware-vmx.exe"

# Disables power throttling for the main VMware Workstation executable
powercfg /powerthrottling disable /path "C:\Program Files (x86)\VMware\VMware Workstation\vmware.exe"
```

---

## VMware tips

**Verify the Monitor Mode**

- To check, open the VM directory in VMware Workstation, locate `vmware.log`, and search for `Monitor Mode:`
- The `vmware.log` should show “Monitor Mode” as `CPL0` instead of `ULM` (Hyper-V)

**Delay the bios boot in a VM**

```bash
# Add line to .vmx file:

bios.bootDelay = "5000"
```





---

