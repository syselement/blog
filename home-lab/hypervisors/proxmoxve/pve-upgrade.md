# Proxmox Upgrade 7 to 8

> - [PVE - Upgrade_from_7_to_8](https://pve.proxmox.com/wiki/Upgrade_from_7_to_8)
> - [PVE - Roadmap 8.0 - known-issues](https://pve.proxmox.com/wiki/Roadmap#8.0-known-issues)

> Open [https://PVE-IP:8006](https://PVE-IP:8006) and Enable `root` user from Permissions/Users menu.
>

- Login with SSH

```bash
ssh <pve-IP>
su
# input "root" password
```

## Prerequisites

- [ ] Upgraded to the latest version of Proxmox VE 7.4 on all nodes.

  - [ ] Ensure your node(s) have correct package repository configuration (web UI, Node -> Repositories) if your pve-manager version isn't at least 7.4-15.

- [ ] N/A Hyper-converged Ceph: upgrade any Ceph Octopus or Ceph Pacific cluster to Ceph 17.2 Quincy before you start the Proxmox VE upgrade to 8.0.

  - [ ] N/A Follow the guide Ceph Octopus to Pacific and Ceph Pacific to Quincy, respectively.

- [ ] N/A Co-installed Proxmox Backup Server: see the Proxmox Backup Server 2 to 3 upgrade how-to
  Reliable access to the node. It's recommended to have access over a host independent channel like iKVM/IPMI or physical access.
  - [ ] N/A If only SSH is available we recommend testing the upgrade on an identical, but non-production machine first.

- [ ] A healthy cluster
- [ ] ~ Valid and tested backup of all VMs and CTs (in case something goes wrong)
- [ ] At least 5 GB free disk space on the root mount point. - 54G

```bash
root@pve:~# df -h
Filesystem            Size  Used Avail Use% Mounted on
udev                   32G     0   32G   0% /dev
tmpfs                 6.3G  2.5M  6.3G   1% /run
/dev/mapper/pve-root  105G   52G   54G  50% /
tmpfs                  32G   46M   32G   1% /dev/shm
tmpfs                 5.0M     0  5.0M   0% /run/lock
/dev/sda2             511M  332K  511M   1% /boot/efi
/dev/fuse             128M   40K  128M   1% /etc/pve
tmpfs                 6.3G     0  6.3G   0% /run/user/1000
```

```bash
root@pve:~# /usr/sbin/lvs -a
  LV                                    VG  Attr       LSize    Pool      Origin Data%  Meta%  Move Log Cpy%Sync Convert
  data                                  pve twi-aotz-- <196.00g                  0.00   0.83                            
  [data_tdata]                          pve Twi-ao---- <196.00g                                                         
  [data_tmeta]                          pve ewi-ao----    2.00g                                                         
  [lvol0_pmspare]                       pve ewi-------    2.00g                                                         
  root                                  pve -wi-ao----  104.75g                                                         
  swap                                  pve -wi-ao----    8.00g                                                         
  base-900-disk-0                       vg1 Vri-a-tz-k   32.00g thinpool1        2.79                                   
  [lvol0_pmspare]                       vg1 ewi-------    2.00g
  
  thinpool1                             vg1 twi-aotz--   <1.09t                  80.10  5.13                            
  [thinpool1_tdata]                     vg1 Twi-ao----   <1.09t                                                         
  [thinpool1_tmeta]                     vg1 ewi-ao----    2.00g                                                         
  vm-100-disk-0                         vg1 Vwi-aotz--   20.00g thinpool1        19.40                                  
  vm-1000-disk-0                        vg1 Vwi-aotz--    8.00g thinpool1        66.51                                  
  vm-1001-disk-0                        vg1 Vwi-a-tz--   16.00g thinpool1        12.08                                  
  vm-1002-disk-0                        vg1 Vwi-aotz--    8.00g thinpool1        94.27                                  
  vm-1003-disk-0                        vg1 Vwi-aotz--    8.00g thinpool1        4.75                                   
  vm-101-disk-0                         vg1 Vwi-aotz--   32.00g thinpool1        68.44                                  
  vm-102-disk-0                         vg1 Vwi-aotz--   24.00g thinpool1        94.18                                  
  vm-103-disk-0                         vg1 Vwi-a-tz--  256.00g thinpool1        45.52                                  
  vm-104-disk-0                         vg1 Vwi-a-tz--   32.00g thinpool1        15.63                                  
  vm-105-disk-0                         vg1 Vwi-a-tz--   32.00g thinpool1        24.31
```

- [ ] Check [known upgrade issues](https://pve.proxmox.com/wiki/Upgrade_from_7_to_8#Known_Upgrade_Issues)



## Actions step-by-step

The following actions need to be carried out from the command line of each Proxmox VE node in your cluster

**Perform the actions via console or ssh; preferably via console to avoid interrupted ssh connections. Do not carry out the upgrade when connected via the virtual console offered by the GUI; as this will get  interrupted during the upgrade.**

Remember to ensure that a valid backup of all VMs and CTs has been created before proceeding.

### Continuously use the **pve7to8** checklist script

A small checklist program named **`pve7to8`** is  included in the latest Proxmox VE 7.4 packages. The program will provide hints and warnings about potential issues before, during and after the  upgrade process.

To run it with **all** checks enabled, execute:

```bash
pve7to8 --full
```

Make sure to run the full checks at least once before the upgrade.

- Run `pve7to8 --full`

```bash
root@pve:~# pve7to8 --full
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_ADDRESS = "it_IT.UTF-8",
	LC_NAME = "it_IT.UTF-8",
	LC_MONETARY = "it_IT.UTF-8",
	LC_PAPER = "it_IT.UTF-8",
	LC_IDENTIFICATION = "it_IT.UTF-8",
	LC_TELEPHONE = "it_IT.UTF-8",
	LC_MEASUREMENT = "it_IT.UTF-8",
	LC_TIME = "it_IT.UTF-8",
	LC_NUMERIC = "it_IT.UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US.UTF-8").
= CHECKING VERSION INFORMATION FOR PVE PACKAGES =

Checking for package updates..
WARN: updates for the following packages are available:
  bind9-host, libx11-xcb1, libmagic-mgc, libmagic1, file, libflac8, libxpm4, libx11-data, proxmox-backup-file-restore, libc6, locales, bind9-dnsutils, libwebp6, libjson-c5, proxmox-backup-client, libc-dev-bin, pve-manager, libc-l10n, bind9-libs, libc-bin, libc-devtools, pve-kernel-5.15, pve-kernel-5.15.116-1-pve, libc6-dev, pve-firewall, libx11-6, libgstreamer-plugins-base1.0-0, dnsutils, linux-libc-dev

Checking proxmox-ve package version..
PASS: proxmox-ve package has version >= 7.4-1

Checking running kernel version..
PASS: running kernel '5.15.107-2-pve' is considered suitable for upgrade.

= CHECKING CLUSTER HEALTH/SETTINGS =

SKIP: standalone node.

= CHECKING HYPER-CONVERGED CEPH STATUS =

SKIP: no hyper-converged ceph setup detected!

= CHECKING CONFIGURED STORAGES =

PASS: storage 'local' enabled and active.
PASS: storage 'local-lvm' enabled and active.
PASS: storage 'lvm-thin-1' enabled and active.
INFO: Checking storage content type configuration..
PASS: no storage content problems found
PASS: no storage re-uses a directory for multiple content types.

= MISCELLANEOUS CHECKS =

INFO: Checking common daemon services..
PASS: systemd unit 'pveproxy.service' is in state 'active'
PASS: systemd unit 'pvedaemon.service' is in state 'active'
PASS: systemd unit 'pvescheduler.service' is in state 'active'
PASS: systemd unit 'pvestatd.service' is in state 'active'
INFO: Checking for supported & active NTP service..
WARN: systemd-timesyncd is not the best choice for time-keeping on servers, due to only applying updates on boot.
  While not necessary for the upgrade it's recommended to use one of:
    * chrony (Default in new Proxmox VE installations)
    * ntpsec
    * openntpd

INFO: Checking for running guests..
WARN: 14 running guest(s) detected - consider migrating or stopping them.
INFO: Checking if the local node's hostname 'pve' is resolvable..
INFO: Checking if resolved IP is configured on local node..
PASS: Resolved node IP '<NODE_IP>' configured and active on single interface.
INFO: Check node certificate's RSA key size
PASS: Certificate 'pve-root-ca.pem' passed Debian Busters (and newer) security level for TLS connections (4096 >= 2048)
PASS: Certificate 'pve-ssl.pem' passed Debian Busters (and newer) security level for TLS connections (2048 >= 2048)
PASS: Certificate 'pveproxy-ssl.pem' passed Debian Busters (and newer) security level for TLS connections (4096 >= 2048)
INFO: Checking backup retention settings..
PASS: no backup retention problems found.
INFO: checking CIFS credential location..
PASS: no CIFS credentials at outdated location found.
INFO: Checking permission system changes..
INFO: Checking custom role IDs for clashes with new 'PVE' namespace..
PASS: no custom roles defined, so no clash with 'PVE' role ID namespace enforced in Proxmox VE 8
INFO: Checking if LXCFS is running with FUSE3 library, if already upgraded..
SKIP: not yet upgraded, no need to check the FUSE library version LXCFS uses
INFO: Checking node and guest description/note length..
PASS: All node config descriptions fit in the new limit of 64 KiB
PASS: All guest config descriptions fit in the new limit of 8 KiB
INFO: Checking container configs for deprecated lxc.cgroup entries
PASS: No legacy 'lxc.cgroup' keys found.
INFO: Checking if the suite for the Debian security repository is correct..
INFO: Checking for existence of NVIDIA vGPU Manager..
PASS: No NVIDIA vGPU Service found.
INFO: Checking bootloader configuration...
SKIP: not yet upgraded, no need to check the presence of systemd-boot

= SUMMARY =

TOTAL:    29
PASSED:   22
SKIPPED:  4
WARNINGS: 3
FAILURES: 0

ATTENTION: Please check the output for detailed information!
```

## Update the configured APT repositories

First, make sure that the system is using the latest Proxmox VE 7.4 packages:

```bash
curl -v https://enterprise.proxmox.com

apt update
apt dist-upgrade
pveversion
```

The last command should report at least `7.4-15` or newer.

### Update Debian Base Repositories to Bookworm

Update all Debian and Proxmox VE repository entries to Bookworm.

```bash
sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list
```

Ensure that there are no remaining Debian Bullseye specific repositories left, if you can use the `#` symbol at the start of the respective line to comment these repositories out. Check all files in the /etc/apt/sources.list.d/pve-enterprise.list and `/etc/apt/sources.list` and see [Package_Repositories](https://pve.proxmox.com/wiki/Package_Repositories) for the correct Proxmox VE 8 / Debian Bookworm repositories.

### Add the Proxmox VE 8 Package Repository

```bash
# BEFORE
root@pve:~# cat /etc/apt/sources.list.d/pve-enterprise.list
deb https://enterprise.proxmox.com/debian/pve bullseye pve-enterprise
```

```bash
echo "deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise" > /etc/apt/sources.list.d/pve-enterprise.list
```

### Refresh Package Index

Update the repositories' package index:

```bash
apt update
```

## Upgrade the system to Debian Bookworm and Proxmox VE 8.0

> ❗️Note that the time required for finishing this step heavily depends on the system's performance, especially the root filesystem's IOPS and bandwidth. A slow spinner can take up to 60 minutes or more, while for a high-performance server with SSD storage, the dist-upgrade can be finished in under 5 minutes.
>

Start with this step, to get the initial set of upgraded packages:

```bash
apt dist-upgrade
```

During the above step, you will be asked to approve changes to  configuration files, where the default config has been updated by their  respective package.

It's suggested to check the difference for each file in question  and choose the answer accordingly to what's most appropriate for your  setup.

📌 Common configuration files with changes, and the recommended choices are:

- ```bash
  /etc/issue
  ```

   -> Proxmox VE will auto-generate this file on boot, and it has only cosmetic effects on the login console.

  ​    Using the default "No" (keep your currently-installed version) is safe here.


- ```bash
  /etc/lvm/lvm.conf
  ```

   -> Changes relevant for Proxmox VE will be updated, and a newer config version might be useful.

  ​    If you did not make extra changes yourself and are unsure it's suggested to choose "Yes" (install the package maintainer's version) here.


- ```bash
  /etc/ssh/sshd_config
  ```

   -> If you have not changed this file manually, the only differences should be a replacement of `ChallengeResponseAuthentication no` with `KbdInteractiveAuthentication no` and some irrelevant changes in comments (lines starting with #).

  ​    If this is the case, both options are safe, though we would recommend installing the package maintainer's version in order to move away from the deprecated ChallengeResponseAuthentication option. If there are other changes, we suggest to inspect them closely and decide accordingly.


- ```bash
  /etc/default/grub
  ```

   -> Here you may want to take  special care, as this is normally only asked for if you changed it  manually, e.g., for adding some kernel command line option.

  It's recommended to check the difference for any relevant change, note that changes in comments (lines starting with #) are not relevant.
  If unsure, we suggested to selected "No" (keep your currently-installed version)


### Check Result & Reboot Into Updated Kernel

If the dist-upgrade command exits successfully, you can re-check the `pve7to8` checker script and reboot the system in order to use the new Proxmox VE kernel.

```bash
pve7to8 --full
```

```bash
root@pve:~# pve7to8 --full
= CHECKING VERSION INFORMATION FOR PVE PACKAGES =

Checking for package updates..
PASS: all packages up-to-date

Checking proxmox-ve package version..
PASS: already upgraded to Proxmox VE 8

Checking running kernel version..
WARN: a suitable kernel (proxmox-kernel-6.2) is intalled, but an unsuitable (5.15.107-2-pve) is booted, missing reboot?!

= CHECKING CLUSTER HEALTH/SETTINGS =

SKIP: standalone node.

= CHECKING HYPER-CONVERGED CEPH STATUS =

SKIP: no hyper-converged ceph setup detected!

= CHECKING CONFIGURED STORAGES =

PASS: storage 'local' enabled and active.
PASS: storage 'local-lvm' enabled and active.
PASS: storage 'lvm-thin-1' enabled and active.
INFO: Checking storage content type configuration..
PASS: no storage content problems found
PASS: no storage re-uses a directory for multiple content types.

= MISCELLANEOUS CHECKS =

INFO: Checking common daemon services..
PASS: systemd unit 'pveproxy.service' is in state 'active'
PASS: systemd unit 'pvedaemon.service' is in state 'active'
PASS: systemd unit 'pvescheduler.service' is in state 'active'
PASS: systemd unit 'pvestatd.service' is in state 'active'
INFO: Checking for supported & active NTP service..
WARN: systemd-timesyncd is not the best choice for time-keeping on servers, due to only applying updates on boot.
  While not necessary for the upgrade it's recommended to use one of:
    * chrony (Default in new Proxmox VE installations)
    * ntpsec
    * openntpd

INFO: Checking for running guests..
PASS: no running guest detected.
INFO: Checking if the local node's hostname 'pve' is resolvable..
INFO: Checking if resolved IP is configured on local node..
PASS: Resolved node IP '<NODE_IP>' configured and active on single interface.
INFO: Check node certificate's RSA key size
PASS: Certificate 'pve-root-ca.pem' passed Debian Busters (and newer) security level for TLS connections (4096 >= 2048)
PASS: Certificate 'pve-ssl.pem' passed Debian Busters (and newer) security level for TLS connections (2048 >= 2048)
PASS: Certificate 'pveproxy-ssl.pem' passed Debian Busters (and newer) security level for TLS connections (4096 >= 2048)
INFO: Checking backup retention settings..
PASS: no backup retention problems found.
INFO: checking CIFS credential location..
PASS: no CIFS credentials at outdated location found.
INFO: Checking permission system changes..
INFO: Checking custom role IDs for clashes with new 'PVE' namespace..
PASS: no custom roles defined, so no clash with 'PVE' role ID namespace enforced in Proxmox VE 8
INFO: Checking if LXCFS is running with FUSE3 library, if already upgraded..
WARN: systems seems to be upgraded but LXCFS is still running with FUSE 2 library, not yet rebooted?
INFO: Checking node and guest description/note length..
PASS: All node config descriptions fit in the new limit of 64 KiB
PASS: All guest config descriptions fit in the new limit of 8 KiB
INFO: Checking container configs for deprecated lxc.cgroup entries
PASS: No legacy 'lxc.cgroup' keys found.
INFO: Checking if the suite for the Debian security repository is correct..
PASS: found no suite mismatch
INFO: Checking for existence of NVIDIA vGPU Manager..
PASS: No NVIDIA vGPU Service found.
INFO: Checking bootloader configuration...
SKIP: proxmox-boot-tool not used for bootloader configuration

= SUMMARY =

TOTAL:    30
PASSED:   24
SKIPPED:  3
WARNINGS: 3
FAILURES: 0

ATTENTION: Please check the output for detailed information!
```

Please note that you should reboot even if you already used the  6.2 kernel previously, through the opt-in package on Proxmox VE 7. This is required to guarantee the best compatibility with the rest of  the system, as the updated kernel was (re-)build with the newer Proxmox  VE 8 compiler and ABI versions.

- Reboot node from GUI
- Install `chrony` to fix the warning:

```bash
root@pve:~# apt install chrony
    Reading package lists... Done
    Building dependency tree... Done
    Reading state information... Done
    The following packages were automatically installed and are no longer required:
      bsdmainutils libboost-coroutine1.74.0 libbpf0 libcbor0 libdns-export1110 libicu67 libisc-export1105 libleveldb1d libmpdec3 libopts25 libperl5.32 libprocps8 libprotobuf23 libpython3.11
      libpython3.9 libpython3.9-minimal libpython3.9-stdlib libtiff5 liburing1 libwebp6 libzpool4linux perl-modules-5.32 python3-ldb python3-talloc python3.9 python3.9-minimal telnet
    Use 'apt autoremove' to remove them.
    Suggested packages:
      networkd-dispatcher
    The following packages will be REMOVED:
      systemd-timesyncd
    The following NEW packages will be installed:
      chrony
    0 upgraded, 1 newly installed, 1 to remove and 0 not upgraded.
    Need to get 288 kB of archives.
    After this operation, 504 kB of additional disk space will be used.
    Do you want to continue? [Y/n] y
    Get:1 http://ftp.it.debian.org/debian bookworm/main amd64 chrony amd64 4.3-2+deb12u1 [288 kB]
    Fetched 288 kB in 0s (761 kB/s)
    (Reading database ... 84561 files and directories currently installed.)
    Removing systemd-timesyncd (252.12-pmx1) ...
    Selecting previously unselected package chrony.
    (Reading database ... 84546 files and directories currently installed.)
    Preparing to unpack .../chrony_4.3-2+deb12u1_amd64.deb ...
    Unpacking chrony (4.3-2+deb12u1) ...
    Setting up chrony (4.3-2+deb12u1) ...

    Creating config file /etc/chrony/chrony.conf with new version

    Creating config file /etc/chrony/chrony.keys with new version
    dpkg-statoverride: warning: --update given but /var/log/chrony does not exist
    Created symlink /etc/systemd/system/chronyd.service → /lib/systemd/system/chrony.service.
    Created symlink /etc/systemd/system/multi-user.target.wants/chrony.service → /lib/systemd/system/chrony.service.
    Processing triggers for dbus (1.14.8-2~deb12u1) ...
    Processing triggers for man-db (2.11.2-2) ...
```

```bash
root@pve:~# pve7to8 --full
= CHECKING VERSION INFORMATION FOR PVE PACKAGES =

Checking for package updates..
PASS: all packages up-to-date

Checking proxmox-ve package version..
PASS: already upgraded to Proxmox VE 8

Checking running kernel version..
PASS: running new kernel '6.2.16-12-pve' after upgrade.
INFO: Found outdated kernel meta-packages, taking up extra space on boot partitions.
      After a successful upgrade, you can remove them using this command:
      apt remove pve-kernel-5.4 pve-kernel-5.15

= CHECKING CLUSTER HEALTH/SETTINGS =

SKIP: standalone node.

= CHECKING HYPER-CONVERGED CEPH STATUS =

SKIP: no hyper-converged ceph setup detected!

= CHECKING CONFIGURED STORAGES =

PASS: storage 'local' enabled and active.
PASS: storage 'local-lvm' enabled and active.
PASS: storage 'lvm-thin-1' enabled and active.
INFO: Checking storage content type configuration..
PASS: no storage content problems found
PASS: no storage re-uses a directory for multiple content types.

= MISCELLANEOUS CHECKS =

INFO: Checking common daemon services..
PASS: systemd unit 'pveproxy.service' is in state 'active'
PASS: systemd unit 'pvedaemon.service' is in state 'active'
PASS: systemd unit 'pvescheduler.service' is in state 'active'
PASS: systemd unit 'pvestatd.service' is in state 'active'
INFO: Checking for supported & active NTP service..
PASS: Detected active time synchronisation unit 'chrony.service'
INFO: Checking for running guests..
WARN: 14 running guest(s) detected - consider migrating or stopping them.
INFO: Checking if the local node's hostname 'pve' is resolvable..
INFO: Checking if resolved IP is configured on local node..
PASS: Resolved node IP '<NODE_IP>' configured and active on single interface.
INFO: Check node certificate's RSA key size
PASS: Certificate 'pve-root-ca.pem' passed Debian Busters (and newer) security level for TLS connections (4096 >= 2048)
PASS: Certificate 'pve-ssl.pem' passed Debian Busters (and newer) security level for TLS connections (2048 >= 2048)
PASS: Certificate 'pveproxy-ssl.pem' passed Debian Busters (and newer) security level for TLS connections (4096 >= 2048)
INFO: Checking backup retention settings..
PASS: no backup retention problems found.
INFO: checking CIFS credential location..
PASS: no CIFS credentials at outdated location found.
INFO: Checking permission system changes..
INFO: Checking custom role IDs for clashes with new 'PVE' namespace..
PASS: no custom roles defined, so no clash with 'PVE' role ID namespace enforced in Proxmox VE 8
INFO: Checking if LXCFS is running with FUSE3 library, if already upgraded..
PASS: systems seems to be upgraded and LXCFS is running with FUSE 3 library
INFO: Checking node and guest description/note length..
PASS: All node config descriptions fit in the new limit of 64 KiB
PASS: All guest config descriptions fit in the new limit of 8 KiB
INFO: Checking container configs for deprecated lxc.cgroup entries
PASS: No legacy 'lxc.cgroup' keys found.
INFO: Checking if the suite for the Debian security repository is correct..
PASS: found no suite mismatch
INFO: Checking for existence of NVIDIA vGPU Manager..
PASS: No NVIDIA vGPU Service found.
INFO: Checking bootloader configuration...
SKIP: proxmox-boot-tool not used for bootloader configuration

= SUMMARY =

TOTAL:    30
PASSED:   26
SKIPPED:  3
WARNINGS: 1
FAILURES: 0

ATTENTION: Please check the output for detailed information!
```



## After the Proxmox VE upgrade

Empty the browser cache and/or force-reload (CTRL + SHIFT + R, or for MacOS ⌘ + Alt + R) the Web UI.

- [ ] Check that all VMs/Containers are up and running

### For Clusters

- Check that all nodes are up and running on the latest package versions.

> Disable `root` user from Proxmox Datacenter Permissions/Users menu

------

