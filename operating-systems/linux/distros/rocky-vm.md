# Rocky Linux

![](.gitbook/assets/rocky.png)

> 📌 *These are Linux commands notes taken from the Rocky Linux docs, maybe I'll move them to a more Linux general section later.*

---

## 🌐 Resources 🔗

> - [Rocky Linux](https://rockylinux.org/)
> - [Rocky Linux Instructional Books - Documentation](https://docs.rockylinux.org/books/)
> - [VI Text editor - Documentation](https://docs.rockylinux.org/books/admin_guide/05-vi/)

### 📚 [The UNIX/Linux Philosophy](https://docs.rockylinux.org/books/admin_guide/01-presentation/#the-unixlinux-philosophy)

- Treat everything as a file.
- Value portability.
- Do one thing and do it well.
- **KISS**: Keep It Simple Stupid.
- "UNIX is a simple operating system, but you have to be a genius to understand the simplicity." (Dennis Ritchie)
- "Unix is user-friendly. It just isn't promiscuous about which users it's friendly with." (Steven King)

---

## Commands

```bash
# command [option(s)] [argument(s)]

/bin/bash
cat /etc/passwd
cat /etc/shadow
cat /etc/profile
cat /etc/sudoers

apropos --exact password -a change
whatis clear
man clear

reboot
shutdown -r +5
shutdown now
shutdown -h 0:30
shutdown -h 0:30 "Server shutdown at 0:30"
shutdown --show
shutdown -c

history
clear
echo -ne "\t Hello \n"
echo $LANG
echo $PATH
date
date +%F
date +%x
date +%T
date -d 20250310 +%j
# Convert timestamp to a date
date -d "1970-01-01 20156 days"
# Convert date to timestamp
echo $(($(date --date="2025-01-25" +%s)/86400+1))

id root
whoami
who
who -r

pwd
cd
ls
ls -l
ls -ld
ls -lX
ls -g
ls -dl
ls -lah
ls -ltr
ls -lhS
ls -lhR
ls -liah
ls -lAhR
ls -lhR /var/ | grep ^\- | grep -E "[1-9]*\.[0-9]*M"
alias
ll
du -sh /etc

mkdir -p /tmp/newdir/newsubdir
touch file
touch -t [AAAA]MMJJhhmm[ss] file
rmdir /tmp/newdir/newsubdir
rm file
rm -r dir
rm -rf dir/files
rm -f -- -file
mv file1 file2
mv file1 file2 /tmp
cp file1 file2
cp file /tmp
cp -pr dir1 dir2

file /etc/passwd
more /etc/passwd
less file1
	/text # search
	g or G # first/last page
cat /etc/passwd
cat -n /etc/passwd
cat -b /etc/profile
cat /etc/passwd /etc/group
tac /var/log/messages | less
head -n 20 /etc/profile
tail -n 20 /etc/profile
tail -f /var/log/messages
sort -k 3,4 -t ":" -n /etc/passwd
sort -u FILE
sort FILE | uniq
sort -nr ips.txt
sort -hr size.txt
wc -c
wc -l

find /tmp -name *.txt -exec rm -f {} \;
find /tmp -name *.txt -delete
find /var -size +1M -a -size -1024M  -a -type f  -exec ls -lh {} \;
whereis ls
which ls
grep "root*" /etc/passwd
grep -w "root:" /etc/passwd
grep -w "^root" /etc/passwd
grep -i version /etc/os-release
grep -iv version /etc/os-release
grep -R "systemd" /var/log/
```

```bash
# Input redirection
ftp -in serverftp << ftp-commands.txt
wc -l /etc/profile
wc -l < /etc/profile

# Output redirection
date +%F > date_file  # overwrite
date +%F >> date_file # append

# stderr output redirection
ls -R / 2> errors_file
ls -R / >> ok_file 2>> nok_file
ls -R / >> log_file 2>&1
ls -R / 2>> /dev/null

# Copy stdin to file and to stdout
cat /etc/os-release | tee release

# Pipes
ls -lia / | head
ls -lia / | tail
ls -lia / | sort
ls -lia / | wc
ls -lia / | grep etc
ls -lia / | tee list

# Aliases
alias
	cat .bashrc		# permanent user's aliases file
	cat /etc/bashrc # all users file
type ls
ls
\ls

mount | column -t
ls /; cd /home; ls -lia; cd /
```

```bash
sort firstnames.txt | uniq
sort firstnames.txt | uniq -cd

xargs ls -ld
    /home
    /tmp
	<CTRL+D>
xargs -L 1 find /var/log -name
xargs -n 1 find /var/log -name
find /var/log/ -name "*.log" -mtime -1 | xargs tar cvfP /root/log.tar
mkdir /tmp/backup && find /var/log -type f -name "*.log" | xargs -I % cp % /tmp/backup

watch
watch -n 5 tail -n 5 /var/log/secure
watch -n 1 'ls -l | wc -l'
watch -t -n 1 date

install -d /tmp/samples
install /tmp/test.txt /tmp/samples/
install -v -o syselement -g users -m 644 -D -t /tmp/samples/ /tmp/test.txt
install -v -b -S ".bak" -D -t /tmp/samples/ /tmp/test.txt

tree
tree -ah
tree -hugp /etc/security/

stat /var/log/messages
```

```bash
# yum/dnf
# yum-utils / dnf-utils package
dnf install dnf-utils

repoquery
repoquery -l yum-utils
rpm -ql yum-utils # same
yumdownloader --destdir /var/tmp/ --resolve samba
dnf download --downloadonly --downloaddir /var/tmp  --resolve  samba # same

# pmisc package
pstree
killall nc
fuser
```

### User management

```bash
# Files
/etc/passwd
/etc/shadow
/etc/group
/etc/gshadow
/etc/skel/
/etc/default/useradd
/etc/login.defs
```

```bash
# Groups
cat /etc/group
cat /etc/gshadow

groupadd -g 1020 group1
# Debian
addgroup

groupmod -g 1022 group1
groupmod -n group3 group2
sudo find / -gid 1020 -exec chgrp 1022 {} \;
groupdel group1

# root GID = 0

gpasswd -a user2 group1
gpasswd -d user2 group1

echo $SHLVL ; echo $BASH_SUBSHELL
newgrp group1 # child shell with temp group1
```

```bash
# Users
# root (uid=0) - system admin
# system users (uid=201~999)
# regulare users (uid>=1000)
cat /etc/passwd
cat /etc/shadow
# ! For each line in the /etc/passwd file there must be a corresponding line in the /etc/shadow file.

# default settings
ls -lah /etc/skel/ # file templates for new user home dir
cat /etc/login.defs
cat /etc/default/useradd

useradd -D # shows /etc/default/useradd contents
useradd user1 # defaults from /etc/default/useradd
useradd -N user1 # no user group, uses the default (users:100)
useradd -u 1002 -g 1020 -d /home/user user2
passwd user1
tail -n 2 /etc/passwd
tail -n 2 /etc/shadow
tail -n 2 /etc/group ; tail -n 2 /etc/gshadow
# Debian
adduser
deluser

usermod -u 1005 user1
usermod -L user2 # lock user
usermod -G user1 user2

userldel -r user1 # deletes user's home dir, mail files and primary group

# File owners
chown user1 file
chown :group1 file
chown user1:group1 file
chown -R -v user1:group1 /tmp/dir
chown --reference=./file2 file
chgrp group1 file
```

```bash
passwd
passwd user1
passwd -S user1
passwd -l user1 # lock user
passwd -u user1 # unlock user
sudo echo "p@ssw0rd" | passwd --stdin user1 # default pw, e.g. in scripts

chage user1
chage -m 60 -M 90 -W 80 -I 10 user1 # pw expire

su # login as root
su - user1 # - = -l = make the shell a login shell
su - root -c "cat /etc/shadow"
```

### File System

| Directory  | Functionality                                                | Complete word                 |
| :--------- | :----------------------------------------------------------- | :---------------------------- |
| `/`        | Contains special directories                                 |                               |
| `/boot`    | Files related to system startup                              |                               |
| `/sbin`    | Commands necessary for system startup and repair             | *system binaries*             |
| `/bin`     | Executables of basic system commands                         | *binaries*                    |
| `/usr/bin` | System administration commands                               |                               |
| `/lib`     | Shared libraries and kernel modules                          | *libraries*                   |
| `/usr`     | Saves data resources related to UNIX                         | *UNIX System Resources*       |
| `/mnt`     | Temporary mount point directory                              | *mount*                       |
| `/media`   | For mounting removable media                                 |                               |
| `/misc`    | To mount the shared directory of the NFS service.            |                               |
| `/root`    | Administrator's login directory                              |                               |
| `/home`    | The upper-level directory of a common user's home directory  |                               |
| `/tmp`     | The directory containing temporary files                     | *temporary*                   |
| `/dev`     | Special device files                                         | *device*                      |
| `/etc`     | Configuration and script files                               | *editable text configuration* |
| `/opt`     | Specific to installed applications                           | *optional*                    |
| `/proc`    | This is a mount point for the proc filesystem, which provides information about running processes and the kernel | *processes*                   |
| `/var`     | This directory contains files which may change in size, such as spool and log files | *variables*                   |
| `/sys`     | Virtual file system, similar to /proc                        |                               |
| `/run`     | That is /var/run                                             |                               |
| `/srv`     | Service Data Directory                                       | *service*                     |

```bash
ls -lah /dev/

# (Standard) Partitions
cat /proc/partitions
fdisk -l
fdisk -l /dev/nvme0n1
cfdisk /dev/nvme0 # optimized, to managed partitions
parted
parted -l /dev/nvme0
gparted # GUI

lsblk
dmsetup ls

# Disk preparation steps (no LVM)
# 1. Physical disk setup
# 2. Volumes partitioning
# 3. File systems creation
# 4. File systems mounting
# 5. User access management
```

| LVM STORAGE<br />MECHANISMS | Performance | Redundancy | Space Efficiency | Use Case                  |
| --------------------------- | ----------- | ---------- | ---------------- | ------------------------- |
| Linear                      | Normal      | ❌ No       | ✅ High           | Basic storage growth      |
| Striped                     | ✅ High      | ❌ No       | ✅ High           | Fast I/O, performance     |
| Mirrored                    | Moderate    | ✅ Yes      | ❌ Low            | Data safety, critical use |

```bash
# LVM - Logical Volume Manager

# PV (Physical Volume): Raw storage (disk/partition)
# PE (Physical Extent): Chunk of space in PVs
# VG (Volume Group): Pool of storage from PVs
# LV (Logical Volume): Usable partitions created from VG
# FS (File System): each LV can be formatted (ext4, xfs, ...)

# PV commands
pvscan
pvcreate
pvdisplay
pvremove
pvs

# VG commands
vgscan
vgcreate
vgdisplay
vgremove
vgextend
vgreduce
vgs

# LV commands
lvscan
lvcreate
lvdisplay
lvremove
lvextend
lvreduce
lvs

# DISK PREPARATION

# 0. Prepare Physical disk and Partitions (if necessary)
# check for existing LVM volumes
pvs
vgs
lvs
# Wipe nvme0n2 disk - WIPES ALL LVM/DATA/HEADERS!
wipefs --all --force /dev/nvme0n2
dd if=/dev/zero of=/dev/nvme0n2 bs=1M count=100
# dd if=/dev/zero of=/dev/nvme0n2 bs=1M status=progress
# ^^^ OWERWRITES ENTIRE DISK (SLOW) ^^^
hexdump -C /dev/nvme0n2 # should be clean

# Create new GPT partition table on disk nvme0n2
parted /dev/nvme0n2 mklabel gpt
# create single primary partition on disk nvme0n2
parted /dev/nvme0n2 mkpart primary 1MiB 100% 

fdisk -l /dev/nvme0n2
# Disk /dev/nvme0n2: 5 GiB, 5368709120 bytes, 10485760 sectors
# Disk model: VMware Virtual NVMe Disk
# Units: sectors of 1 * 512 = 512 bytes
# Sector size (logical/physical): 512 bytes / 512 bytes
# I/O size (minimum/optimal): 512 bytes / 512 bytes
# Disklabel type: gpt
# Disk identifier: 34A69BE9-CA31-4ADC-B30A-AE22BB47A281
# 
# Device         Start      End  Sectors Size Type
# /dev/nvme0n2p1  2048 10483711 10481664   5G Linux filesystem

# ------ 1. LVM Physical Volume (no partitions case) ------
lsblk
pvcreate /dev/nvme0n2p1
pvdisplay /dev/nvme0n2p1
# --- NEW Physical volume ---
# PV Name               /dev/nvme0n2p1
# VG Name
# PV Size               <5.00 GiB
# Allocatable           NO
# PE Size               0
# Total PE              0
# Free PE               0
# Allocated PE          0
# PV UUID               b6bQp6-57SG-vTST-wu3r-F2r7-z5ML-FHHV8W

# ------ 2. LVM Volume Group ------
vgcreate vol2 /dev/nvme0n2p1
vgdisplay /dev/vol2
# --- Volume group ---
# VG Name               vol2
# System ID
# Format                lvm2
# Metadata Areas        1
# Metadata Sequence No  1
# VG Access             read/write
# VG Status             resizable
# MAX LV                0
# Cur LV                0
# Open LV               0
# Max PV                0
# Cur PV                1
# Act PV                1
# VG Size               <5.00 GiB
# PE Size               4.00 MiB
# Total PE              1279
# Alloc PE / Size       0 / 0
# Free  PE / Size       1279 / <5.00 GiB
# VG UUID               cGPnl2-e1Xx-JF7l-uS8r-KmAa-NwX9-LLcxFo

# ------ 3. LVM Logical Volume ------
# lvcreate -L 5G -n lv2 vol2
lvcreate -l 100%FREE -n lv2 vol2
lvdisplay /dev/vol2/lv2
# --- Logical volume ---
# LV Path                /dev/vol2/lv2
# LV Name                lv2
# VG Name                vol2
# LV UUID                DZ3CCM-twpf-arLX-JYt7-9BQE-zoKq-Os5NxE
# LV Write Access        read/write
# LV Creation host, time rockyvm, 2025-05-01 10:00:40 +0200
# LV Status              available
# # open                 0
# LV Size                <5.00 GiB
# Current LE             1279
# Segments               1
# Allocation             inherit
# Read ahead sectors     auto
# - currently set to     8192
# Block device           253:2

# ------ 4. File System creation/mounting ------
# ext2, ext3, ext4, FAT16, FAT32, NTFS, HFS, BtrFS, JFS, XFS, ...
mkfs -t xfs /dev/vol2/lv2
blkid /dev/vol2/lv2
# /dev/vol2/lv2: UUID="fc6f54cc-e718-45e4-9620-169bdcd143ce" TYPE="xfs"
mkdir -p /mnt/lv2
echo 'UUID=fc6f54cc-e718-45e4-9620-169bdcd143ce  /mnt/lv2  xfs  defaults  0 2' | tee -a /etc/fstab
systemctl daemon-reload
mount -a
df -hT /mnt/lv2
# Filesystem           Type  Size  Used Avail Use% Mounted on
# /dev/mapper/vol2-lv2 xfs   5.0G   68M  4.9G   2% /mnt/lv2

# Final check Disk -> PV -> VG -> LV -> FS
echo "=== LVM ===" && pvs && vgs && lvs && echo "=== Disks ===" && lsblk -f && echo "=== Mounts ===" && findmnt
```

```bash
# Boot sector -> Super Block -> inodes table -> Data block

# In Linux, everything is a file.
# Linux meets the FHS (Filesystems Hierarchy Standard)
man hier

# Check file system consistency - partition must be UNmounted
umount /mnt/lv2
fsck -V /dev/vol2/lv2
xfs_repair -v /dev/vol2/lv2

# Mount point automatically mounted at boot time
cat /etc/fstab
# /dev/mapper/rl-root     /                       xfs     defaults        0 0
# UUID=652d402b-d797-4b8f-94f1-ba39a3537811 /boot                   xfs     defaults        0 0
# /dev/mapper/rl-swap     none                    swap    defaults        0 0
# UUID=fc6f54cc-e718-45e4-9620-169bdcd143ce  /mnt/lv2  xfs  defaults  0 2

# UUID = Universally Unique Identifier
lsblk -o name,uuid

# Mount all filesystems mentioned in /etc/fstab
systemctl daemon-reload
mount
mount -a
# mounted info written to /etc/mtab
cat /etc/mtab
mount

man 8 mount
# mount -o defaults = mount -o rw,suid,dev,exec,auto,nouser,async
umount /mnt/lv2

# e.g. Temporary mount
mkdir -p /mnt/usb
mount -t vfat /dev/sdb1 /mnt/usb
cd /mnt/usb/
umount /mnt/usb
```

```bash
# ------ File naming ------
mkdir "dir_1"
touch .hidden_file

# .c : source file in C language
# .h : C and Fortran header file
# .o : object file in C language
# .tar : data file archived with the tar utility
# .cpio : data file archived with the cpio utility
# .gz : data file compressed with the gzip utility
# .tgz : data file archived with the tar utility and compressed with the gzip utility
# .html : web page

ls -liah /usr/bin/passwd
# 9317257 -rwsr-xr-x. 1 root root 32K May 15 2022 /usr/bin/passwd
#  1      2    3      4  5    6    7   8            9

# 1	Inode number
# 2	File type (1st character of the block of 10). "-" = ordinary file
# 3	Access rights (last 9 characters of the block of 10)
# 4	Directories: entry/subdirs count. Files: hard link count (1 = single link)
# 5	Owner name
# 6	Group name
# 7	Size (byte, kilo, mega)
# 8	Date of last update
# 9	Name of the file

# ------ 7 File types in GNU/Linux ------
# . Current directory  
# .. Parent directory 
# - Regular file (text, binary, data, compressed)
# d Directory
# b Block device (disks, USBs)
# c Character device (keyboard, mouse)
# s Socket (network IPC)
# p Pipe/FIFO (inter-process communication). FIFO = First-In-First-Out
# l Symbolic/soft/hard link (like a shortcut)

ls -ld
# dr-xr-x---. 3 root root 4096 May  1 11:57 .
ls -la
# dr-xr-x---.  3 root root  4096 May  1 11:57 .
# dr-xr-xr-x. 18 root root   235 Mar  9 14:37 ..

ls -l /dev/nvme0n1
# brw-rw----. 1 root disk 259, 0 May  1 09:29 /dev/nvme0n1

ls -l /dev/tty
# crw-rw-rw-. 1 root tty 5, 0 May  1 08:28 /dev/tty

ls -l /etc/grub2.cfg
# lrwxrwxrwx. 1 root root 22 Mar 18 12:41 /etc/grub2.cfg -> ../boot/grub2/grub.cfg

# ------ e.g. Soft and Hard links ------
touch original.txt
# Create a soft (symbolic) link
ln -s original.txt softlink.txt
ls -l
    # -rw-r--r--. 1 root root     0 May  1 17:59 original.txt
    # lrwxrwxrwx. 1 root root    12 May  1 18:00 softlink.txt -> original.txt
# Delete original file, soft link breaks
rm original.txt
ls -l
# softlink.txt points to nothing (broken, in red)

# Create a hard link
touch fileA
ln fileA hardlinkA
ls -li
# Same inode, hardlinkA and fileA are indistinguishable
    # 8835024 -rw-r--r--. 2 root root     0 May  1 18:02 fileA
    # 8835024 -rw-r--r--. 2 root root     0 May  1 18:02 hardlinkA
# Remove one, the other still works
rm fileA
cat hardlinkA
# File content still exists
# Hard link to directories is not allowed
```

```bash
# ------ File attributes ------
# Basic file/directory permissions
# r / 4 = read       (file: view, dir: list)
# w / 2 = write      (file: edit, dir: create/delete)
# x / 1 = execute    (file: run, dir: cd into)
# - / 0 = no right/access

ls -l   # -rwxr-xr--
#          └──┴──┴── owner, group, others

# User types
# u  = owner
# g  = owner group
# o  = others users
# a  = all (u+g+o)

# ------ Octal representation ------
#            USER       GROUP      OTHER
#          +--------+ +--------+ +--------+
# Read (r) |   4    | |   4    | |   4    |
# Write(w) |   2    | |   0    | |   0    |
# Exec (x) |   1    | |   1    | |   1    |
#          +--------+ +--------+ +--------+
# Total:   4+2+1 = 7   4+0+1 = 5   4+0+1 = 5
# Octal permission: 755 → rwxr-xr-x
chmod 755 file.sh
# rwxr-xr-x
chmod -R 644 /data
# recursive, files: rw-r--r--

# ------ Symbolic representation ------
# Who?        Symbol
# ---------------------
# User         u
# Group        g
# Other        o
# All          a
# 
# Operation   Symbol
# ---------------------
# Add          +
# Remove       -
# Replace      =
# 
# Rights      Symbol
# ---------------------
# Read         r
# Write        w
# Execute      x
chmod u+x file
# add execute to owner
chmod u+rwx,g+wx,o-r file
# remove write from group, read from others
chmod -R a=r folder/
# add read for all recursively

# ------ Rights and mask (default permissions) ------
# Default Permissions Calculation
# --------------------------------------------------------------------------------
# | Type       | Max Rights | Umask | Effective Rights | -x Bits | Final Rights |
# |------------|------------|-------|------------------|---------|---------------|
# | Directory  | 777        | 022   | 755 (rwxr-xr-x)  |   -     | 755           |
# | File       | 666*       | 022   | 644 (rw-r--r--)  | remove x| 644           |
# --------------------------------------------------------------------------------
# 
# * Note: Max rights for files is 666 (rw-rw-rw-) because execute (x) is not granted
#         by default when creating a file, for security reasons.
# 
# Explanation:
# - Max Rights: theoretical full permissions (777 for dirs, 666 for files)
# - Umask: subtracts permission bits (e.g. 022 removes write for group/others)
# - -x Bits: execution bits are removed from files even if umask allows them
# - Final Rights: resulting permissions on creation
umask
man umask
help umask
# 0022 = permissions 755 (rwxr-xr-x)
umask -S
# u=rwx,g=rx,o=rx - symbolic/file rights

# e.g.
umask 027
# new files for current session: 640, dirs: 750
touch test.txt
ls -l test.txt
# -rw-r-----

# /etc/login.defs file defines the default UMASK
grep -Ri umask /etc/login.defs

# Config persistence for custom umask
# ~/.bashrc (particular user) 
# /etc/bashrc and /etc/profile (all users)
# ^^^ these overrides the umask parameter of /etc/login.defs
```



---

## Terminal Keyboard shortcuts

> - [bash - readline interaction](https://www.gnu.org/software/bash/manual/html_node/Readline-Interaction.html)
> - [21 Useful Linux Terminal Shortcuts Pro Users Love](https://itsfoss.com/linux-terminal-shortcuts/)

```bash
Ctrl + A	# Move to the start of the line.
Ctrl + E	# Move to the end of the line.
Ctrl + U	# Delete from the cursor to the start of the line.
Ctrl + K	# Delete from the cursor to the end of the line.
Ctrl + W	# Delete the word before the cursor.
Ctrl + L	# Clear the terminal screen.
Ctrl + C	# Stop the current process/command.
Ctrl + D	# Log out or exit the terminal.
Ctrl + Z	# Pause the current process (can be resumed).
Ctrl + R	# Search command history (backward search).

Up Arrow	# Show the previous command (from the command history).
Down Arrow	# Show the next command (from the command history).

!!			# Repeat the last command.
!n			# Repeat the nth command from history.

Tab			# Auto-complete commands, files, or directories.
Tab twice	# List all possible completions.

Ctrl + Shift + C	# Copy the selected text or command.
Ctrl + Shift + V	# Paste copied text or command.
Ctrl + Shift + N	# Open a new terminal window.
Ctrl + Shift + T	# Open a new tab in the terminal.

Ctrl + Tab or Ctrl + PageDown	# Switch between terminal tabs.
```





---

## Tools

### vi/[vim](https://www.vim.org/)

```bash
vi

```

