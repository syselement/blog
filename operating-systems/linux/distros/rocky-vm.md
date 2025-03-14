# Rocky Linux

![](.gitbook/assets/rocky.png)

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
addgroup # Debian

groupmod -g 1022 group1
groupmod -n group3 group2
sudo find / -gid 1020 -exec chgrp 1022 {} \;
groupdel group1

# root GID = 0
```

```bash
# Users

userldel -r # deletes user's primary group too
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

