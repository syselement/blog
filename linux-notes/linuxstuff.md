# 📃Linux Stuff

![](.gitbook/assets/Linux-logo_small.png)

> 📜 In this 🐧 **Linux** notes repository, I store all of my notes related to the Linux operating system and Unix based apps.

* 📌_Some commands can be outdated._
* ❗_Most of the commands are for **Debian-based** distributions._

## Terminal commands

### Linux Kernel version

```bash
uname -srm
```

### Linux Distribution

```bash
cat /etc/*release
```

### Cpu - Ram usage

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

### Check Swap  usage

```bash
cat /proc/swaps
swapon -s
grep Swap /proc/meminfo
free -m

htop
```

### Terminal clean

```bash
clear
# or CTRL+L
```

### System off

```bash
sudo shutdown now
```

### General update

```bash
sudo apt update && sudo apt upgrade -y && sudo apt clean -y && sudo apt autoremove -y
sudo apt dist-upgrade

sudo apt clean && sudo apt autoremove
sudo apt autoclean
sudo apt --purge autoremove
```

* Info:
  * **apt clean** → cleans the packages and install script in /var/cache/apt/archives/ (_removes all stored archives in your cache_)
  * **apt autoclean** → cleans obsolete deb-packages, _**less than clean**_ (_removes all stored archives in your cache for packages that can not be downloaded anymore_ (thus packages that are no longer in the repo or that have a newer version in the repo))
  * **apt autoremove** → _removes orphaned packages which are not longer needed from the system_, but not purges them, use the --purge option together with the command for that.
  * **apt --purge autoremove** → remove config files and (more important as it cleans dead subdirectories from the documentation tree) entries from /usr/share/doc.

### Create Aliases

- Ubuntu:

```bash
nano ~/.bashrc
# Append the following text with personal commands. O.S. update in this case:

alias updateme='sudo -- sh -c "sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y"'

# Load changes:
source ~/.bashrc

# List all aliases:
alias
```

- Kali Linux:

```bash
nano ~/.zshrc
# Append the following text with personal commands. O.S. update in this case:

alias updateme='sudo -- sh -c "sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y"'

# Load changes:
source ~/.zshrc

# List all aliases:
alias
```

### Set local timezone

```bash
# Gui:
sudo unlink /etc/localtime
sudo ln -s /usr/share/zoneinfo/Europe/Rome /etc/localtime
sudo timedatectl set-timezone "Europe/Rome"

sudo dpkg-reconfigure keyboard-configuration
```

### Install packages (deb, etc)

```bash
sudo dpkg -i packname
```

### First settings after O.S. install

```bash
passwd root
passwd kali
sudo apt install gnome-tweaks -y
sudo apt install tor -y
sudo apt install vlc -y
sudo apt install terminator -y
sudo update-alternatives --config x-terminal-emulator
# gsettings set org.gnome.desktop.default-applications.terminal exec 'terminator'
```

### Set grub boot menu timeout

```bash
sudo nano /etc/default/grub

# Set the seconds in the GRUB_TIMEOUT value, save and exit the file
GRUB_TIMEOUT=1

sudo update-grub
# Reboot to test the timeout

# To instantly boot without the grub menu waiting, edit the file adding these lines:
GRUB_HIDDEN_TIMEOUT=0
GRUB_HIDDEN_TIMEOUT_QUIET=true
GRUB_TIMEOUT=0
```

### Boot Partition Clean Up (Ubuntu/Debian)

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

### Change Console setup

```bash
# For italian keyboard:
sudo dpkg-reconfigure console-setup
```

### Disable Ubuntu automatic updates

```bash
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
# make sure all these directives are set to “0”

sudo systemctl disable apt-daily-upgrade.timer
sudo systemctl mask apt-daily-upgrade.service
sudo systemctl disable apt-daily.timer
sudo systemctl mask apt-daily.service
```

### Speedtest-cli

```bash
wget -O /dev/null -q --show-progress https://speed.hetzner.de/10GB.bin
```

- Or install `speedtest-cli`

```bash
sudo apt install speedtest-cli
```

```bash
speedtest-cli --secure
```

### [Speedtest by Ookla](https://www.speedtest.net/apps/cli)

```bash
sudo apt install curl
curl -s https://packagecloud.io/install/repositories/ookla/speedtest-cli/script.deb.sh | sudo bash
sudo apt install speedtest
```

```bash
speedtest
```

### Change password at boot - Single User Mode

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

### Check Hardware Temperatures

```bash
sudo apt install lm-sensors hddtemp
sudo sensors-detect
/etc/init.d/kmod start
sensors

# Raspberry Pi Temp
vcgencmd measure_temp
```

## Installations

### Basic Tools

```bash
sudo apt install -y wget net-tools htop tree terminator flameshot
```

### Install Sublime

```bash
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/sublimehq-archive.gpg > /dev/null
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
sudo apt update && sudo apt install sublime-text -y
```

### Install Obsidian

```bash
# Download Obsidian latest release from https://github.com/obsidianmd/obsidian-releases/releases
mv Obsidian-0.12.4.AppImage usr/local/bin/obsidiann
chmod +x usr/local/bin/obsidian
```

### Install Brave Browser

```bash
sudo apt install curl

sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg arch=amd64] https://brave-browser-apt-release.s3.brave.com/ stable main"|sudo tee /etc/apt/sources.list.d/brave-browser-release.list

sudo apt update

sudo apt install brave-browser
```

### Install Anydesk

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

### Install DnsLeakTest

```bash
cd /tmp
wget https://raw.githubusercontent.com/macvk/dnsleaktest/master/dnsleaktest.py
chmod +x dnsleaktest.py
./dnsleaktest.py
```

### Install Tor

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

### Install Anonsurf

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

### [Install Metasploit Framework](https://docs.metasploit.com/docs/using-metasploit/getting-started/nightly-installers.html)

```bash
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall && chmod 755 msfinstall && ./msfinstall
```

- Or use APT package manager on Debian-based distros

```bash
sudo apt update && sudo apt install metasploit-framework -y
```

- Enable `postgresql` at boot, start the service and initialize MSF database

```bash
sudo systemctl enable postgresql
sudo systemctl restart postgresql
sudo msfdb init
```

- Run **`msfconsole`** to start the Metasploit Framework Console

```bash
msfconsole
```

### [Install Nessus](tools/Nessus.md)

### Install Asbru CM (Linux Desktop)

* Link [Asbru](https://www.asbru-cm.net/)
* _Ásbrú Connection Manager_ is a user interface that helps organizing remote terminal sessions and automating repetitive tasks.

```bash
curl -1sLf 'https://dl.cloudsmith.io/public/asbru-cm/release/cfg/setup/bash.deb.sh' | sudo -E bash
sudo apt install asbru-cm
```

### Install Burpsuite

- Link [Burpsuite Download](https://portswigger.net/burp/communitydownload)
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

## Usage and Configuration

### Configure Git for Github

```bash
git config --global user.email "YOUR_EMAIL@gmail.com"
git config --global user.name"YOUR_USERNAME"

ssh-keygen -t ed25519 -C "YOUR_EMAIL@gmail.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
xclip -selection clipboard < ~/.ssh/id_ed25519.pub

# Add a new key on github.com and paste the one copied with xclip.
# Test it:
ssh -T git@github.com

git remote set-url origin git@github.com:syselement/vaults.git
```

### Proxychains

```bash
#Edit proxychains:
nano /etc/proxychains.conf
# uncoment dynamic_chain
# coment strict_chain
# uncoment Proxy DNS requests-no leak for DNS data
# add on proxylist: socks5 127.0.0.1 9050
```

### OpenVpn

```bash
# TryHackme OpenVpn Cypher(Temporary) error fix 
sed -i 's/cipher AES-256-CBC/data-ciphers AES-256-CBC/' yournickname.ovpn

# or
sudo nano yournickname.ovpn
# Substitute line 14 "cipher AES-256-CBC" with:
data-ciphers AES-256-CBC
```

```bash
# Launch openvpn connection in background as a daemon
sudo openvpn --config yournickname.ovpn --daemon

# Stop it
sudo pkill -f openvpn
```

### NMAP

```bash
nmap 192.168.254.129
nmap 192.168.254.1/24
nmap 192.168.254.1-100
nmap -p80,21,23 192.168.254.129
```

## Virtual Machines

### METASPLOITABLE VM

> 📌 Check **Metasploitable3** VM [here](../home-lab/redteam/metasploitable3.md)

* [Metasploitable2 - Download link](https://sourceforge.net/projects/metasploitable/files/Metasploitable2/)

* Guides:
  * [Metasploitable 2](https://docs.rapid7.com/metasploit/metasploitable-2/)
  * [Metasploitable 2 Exploitability Guide](https://docs.rapid7.com/metasploit/metasploitable-2-exploitability-guide/)

```bash
# Login: msfadmin:msfadmin
```
