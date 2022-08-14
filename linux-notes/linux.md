# Linux

![](.gitbook/assets/Linux-logo_small.png)

> 📜 This is my Linux notes repository, where I keep any notes I have about the Linux operating system and Unix programs.

- *Some commands can be outdated.*

# Terminal commands

## Linux Kernel version

```bash
uname -srm
```

## Linux Distribution

```bash
cat /etc/*release
```

## Cpu - Ram usage

```bash
top
iostatmpstat
vmstat 1 5
mpstat -P ALL 2 4
sar -P ALL 2 3

sudo apt-get install htop
htop

sudo apt-get install nmon
nmon
```

## Terminal clean

```bash
clear
# or CTRL+L
```

## System off

```bash
sudo shutdown now
```

## General update

```bash
sudo apt update && sudo apt upgrade -y && sudo apt clean -y && sudo apt autoremove -y
sudo apt dist-upgrade

sudo apt clean && sudo apt autoremove
sudo apt autoclean
sudo apt --purge autoremove
```

- Info:

  - **apt clean** → cleans the packages and install script in /var/cache/apt/archives/ (*removes all stored archives in your cache*)

  - **apt autoclean** → cleans obsolete deb-packages, ***less than clean*** (*removes all stored archives in your cache for packages that can not be downloaded anymore* (thus packages that are no longer in the repo or that have a newer version in the repo))

  - **apt autoremove** → *removes orphaned packages which are not longer needed from the system*, but not purges them, use the --purge option together with the command for that.

  - **apt --purge autoremove** → remove config files and (more important as it cleans dead subdirectories from the documentation tree) entries from /usr/share/doc.

## Install packages (deb, etc)

```bash
sudo dpkg -i packname
```

## First  settings after O.S. install

```bash
passwd root
passwd kali
sudo apt install gnome-tweaks -y
sudo apt install tor -y
sudo apt install vlc -y
sudo apt install terminator -y
gsettings set org.gnome.desktop.default-applications.terminal exec 'terminator'
```

## Boot Partition Clean Up (Ubuntu/Debian)

```bash
# Check Current Kernel Version
sudo uname -r
# Example output: 5.4.0-64-generic
# As you see on image in my case – 5.4.0-64-generic is current kernel. Other older kernel versions can be deleted.

# List Old installed Kernel images:
sudo dpkg --list 'linux-image*'|awk '{ if ($1=="ii") print $2}'|grep -v `uname -r`

# Your output will something like:
linux-image-5.4.0-39-generic
linux-image-5.4.0-42-generic
linux-image-5.4.0-54-generic
linux-image-5.4.0-64-generic

# To cleanup boot partition in Ubuntu uninstall old kernels with apt command one by one:
sudo apt-get purge linux-image-5.4.0-39-generic
sudo apt-get purge linux-image-5.4.0-42-generic
sudo apt-get purge linux-image-5.4.0-54-generic

# Remove no longer used dependencies:
sudo apt-get autoremove

# Update GRUB:
sudo update-grub
```

## Change Console setup

```bash
# For italian keyboard:
sudo dpkg-reconfigure console-setup
```

## Disable Ubuntu automatic updates

```bash
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
# make sure all these directives are set to “0”

sudo systemctl disable apt-daily-upgrade.timer
sudo systemctl mask apt-daily-upgrade.service
sudo systemctl disable apt-daily.timer
sudo systemctl mask apt-daily.service
```

## Speedtest

```bash
wget -O /dev/null -q --show-progress https://speed.hetzner.de/10GB.bin
```

```bash
sudo apt install wget -y
cd /tmp
wget -O speedtest-cli https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py
chmod +x speedtest-cli
./speedtest-cli
```

## Change password at boot - Single User Mode

```bash
# Start the Pc
# Press E in the grub menu
# Find the "linux /...vmlinuz" kernel line
# Add "init=/bin/bash" at the end of the line
# Press CTRL+X to boot.
# In root prompt, type 
mount -o remount,rw /
# Set a new password for a user with
passwd user
# or for the root user with
passwd
# Reboot
reboot -f
```

## Check Hardware Temperatures

```bash
sudo apt install lm-sensors hddtemp
sudo sensors-detect
/etc/init.d/kmod start
sensors

# Raspberry Pi Temp
vcgencmd measure_temp
```

# Installations

## Install Sublime

```bash
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
sudo apt-get install apt-transport-https
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
sudo apt-get update
sudo apt-get install sublime-text
```

## Install Obsidian

```bash
Download Obsidian latest release from https://github.com/obsidianmd/obsidian-releases/releases
mv Obsidian-0.12.4.AppImage usr/local/bin/obsidiann
chmod +x usr/local/bin/obsidian
```

## Install Anydesk

```bash
# Run the following commands as root user:
# add repository key to Trusted software providers list
wget -qO - https://keys.anydesk.com/repos/DEB-GPG-KEY | apt-key add -

# add the repository:
sudo echo "deb http://deb.anydesk.com/ all main" > /etc/apt/sources.list.d/anydesk-stable.list

# update apt cache:
sudo apt update

# install anydesk:
sudo apt install anydesk -y
```

## Install DnsLeakTest

```bash
cd /tmp
wget https://raw.githubusercontent.com/macvk/dnsleaktest/master/dnsleaktest.py
chmod +x dnsleaktest.py
./dnsleaktest.py
```

## Install Tor

```bash
sudo apt install tor
service tor start
service tor status
-------------------------
Start browser with proxychain:
proxychains firefox www.duckduckgo.com
	(do not close terminal)

search for: check dns leaks
```

## Install Anonsurf

```bash
cd /tmp
git clone https://github.com/Und3rf10w/kali-anonsurf
cd kali-anonsurf/
./installer.sh
clear
cd

anonsurf --help
anonsurf start

anonsurf myip
```

## Install Nessus

- Link: https://docs.tenable.com/nessus/10_3/Content/GetStarted.htm

```bash
sudo dpkg -i Nessus-10.1.1-debian6_amd64.deb
systemctl start nessusd.service

# Update Nessus:
sudo systemctl stop nessusd.service
sudo /opt/nessus/sbin/nessuscli update
```

## Install Asbru CM (Linux Desktop)

- Link: https://www.asbru-cm.net/
- *Ásbrú Connection Manager* is a user interface that helps organizing remote terminal sessions and automating repetitive tasks.

```bash
curl -1sLf 'https://dl.cloudsmith.io/public/asbru-cm/release/cfg/setup/bash.deb.sh' | sudo -E bash
sudo apt install asbru-cm
```

## Install Burpsuite

- Used to update Burpsuite too

```bash
# Download Burp Suite community edition for Linux 64 bits https://portswigger.net/burp/communitydownload

cd /home/user/Downloads
chmod +x ./burpsuite_community_linux_*.sh
sudo ./burpsuite_community_linux_*.sh

# Confirm to update or install the new version in the /opt/tools/burpsuite directory.

sudo nano /usr/bin/burpsuite
    #!/bin/sh
    /opt/tools/burpsuite/jre/bin/java --illegal-access=permit -noverify -jar /opt/tools/burpsuite/burpsuite_community.jar

sudo chmod +x /usr/bin/burpsuite

# Start burspsuite from shell.
```

# Usage and Configuration

## Configure Git for Github

```bash
git config --global user.email "alexideac@gmail.com"
git config --global user.name"syselement"

ssh-keygen -t ed25519 -C "alexideac@gmail.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
xclip -selection clipboard < ~/.ssh/id_ed25519.pub

# Add a new key on github.com and paste the one copied with xclip.
# Test it:
ssh -T git@github.com

git remote set-url origin git@github.com:syselement/vaults.git
```

## Proxychains

```bash
#Edit proxychains:
nano /etc/proxychains.conf
# uncoment dynamic_chain
# coment strict_chain
# uncoment Proxy DNS requests-no leak for DNS data
# add on proxylist: socks5 127.0.0.1 9050
```

## OpenVpn

```bash
# (Check use of update-resolv-conf.sh before openvpn)
openvpn vpnbook-us2-udp25000.ovpn
```

## NMAP

```bash
nmap 192.168.254.129
nmap 192.168.254.1/24
nmap 192.168.254.1-100
nmap -p80,21,23 192.168.254.129
```

# Virtual Machines

## METASPLOITABLE VM

- Download link:
  - http://sourceforge.net/projects/metasploitable/files/Metasploitable2/)

- Guides:
    - https://metasploit.help.rapid7.com/docs/metasploitable-2

    - https://metasploit.help.rapid7.com/docs/metasploitable-2-exploitability-guide


```bash
# Login: msfadmin:msfadmin
```

