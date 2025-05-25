# 📃 Everything Linux

![](.gitbook/assets/Linux-logo-small.png)

> 📜 In this 🐧 **Linux** notes repository, I store all of my notes related to the Linux operating system and Unix based apps.
>
> - 📌*Some commands can be outdated.*
> - ❗*Most of the commands are for **Debian-based** distributions.*

---

## 🌐 Resources 🔗

> - [DistroWatch.com](https://distrowatch.com/)
> - [modern-unix: A collection of modern/faster/saner alternatives to common unix commands.](https://github.com/ibraheemdev/modern-unix)

---

## Terminal commands

### Linux Kernel version

```bash
uname -srm
```

### Linux Distribution

```bash
cat /etc/*release
```

### Linux OS Install date

```bash
stat -c %w /

stat / | grep "Birth" | sed 's/Birth: //g' | cut -b 2-11
stat / | awk '/Birth: /{print $2}'
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

```bash
# Check installed hw RAM memory
sudo dmidecode --type memory
```

### Check Swap usage

```bash
cat /proc/swaps
swapon -s
grep Swap /proc/meminfo
free -m

htop
```

### Swap Enable

- To enable swap with `btrfs` disk run

```bash
sudo btrfs filesystem mkswapfile --size 4G /swapfile
sudo swapon /swapfile
```

### Disk - Expand Partition

```bash
# List all disks and partitions
sudo fdisk -l
lsblk

# Manually verify: which device is your main disk, usually /dev/sda or /dev/nvme0n1
# Manually verify: your Linux root partition, usually /dev/sda1

# growpart may fail to resize /dev/sda1 if no adjacent free space exists; in this case, the swap and extended partitions must be removed.

# 1. Turn off swap
sudo swapoff -a

# 2. Delete swap partition and extended container (replace 5 and 2 with actual numbers if different!)
sudo parted /dev/sda --script rm 5
sudo parted /dev/sda --script rm 2
# If old swap partition UUID is still referenced in initramfs, delete the "resume" file and rebuild initramfs again
sudo rm /etc/initramfs-tools/conf.d/resume
sudo update-initramfs -u

# 3. Expand the root partition
sudo growpart /dev/sda 1

# 4. Check and fix filesystem
sudo e2fsck -f /dev/sda1

# 5. Resize filesystem to fill partition
sudo resize2fs /dev/sda1

# 6. Re-Create a new swapfile (optional but recommended)
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo sh -c "echo '/swapfile swap swap defaults 0 0' >> /etc/fstab"

# 7. Reboot and verify disk and partitions
sudo reboot
sudo fdisk -l
lsblk
df -h
free -h
```

### Bash history

```bash
nano ~/.bashrc
```

```bash
# Append every command to the history file immediately
export PROMPT_COMMAND='history -a'

# Optionally: reload history from the file in case other sessions wrote to it
shopt -s histappend
export HISTFILESIZE=10000
export HISTSIZE=100000
```



### Disk - Expand LVM Partition

```bash
sudo -i
echo 1>/sys/class/block/sda/device/rescan
# cfdisk
# Resize the wanted partition
sudo lsblk
sudo vgdisplay -v
sudo pvresize /dev/<your_sda>
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
sudo vgdisplay -v
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
df -hT
```

### Hardware Check

```bash
inxi -F
```

### ACPI Powerstate

```bash
sudo dmesg | grep ACPI | grep supports
```

> 🔗 [System Sleep States](https://www.kernel.org/doc/html/next/admin-guide/pm/sleep-states.html)
>
> ```bash
> State:		Suspend-To-Idle
> ACPI state:	S0
> Label:		"s2idle" ("freeze")
>
> State:		Standby / Power-On Suspend
> ACPI State:	S1
> Label:		"shallow" ("standby")
>
> State:		Suspend-to-RAM (STR)
> ACPI State:	S3
> Label:		"deep"
>
> State:		Suspend-to-disk (STD/Hibernation)
> ACPI State:	S4
> Label:		"disk"
> ```

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

> Info:
>
> - **apt clean** → cleans the packages and install script in /var/cache/apt/archives/ (_removes all stored archives in your cache_)
> - **apt autoclean** → cleans obsolete deb-packages, _**less than clean**_ (_removes all stored archives in your cache for packages that can not be downloaded anymore_ (thus packages that are no longer in the repo or that have a newer version in the repo))
> - **apt autoremove** → _removes orphaned packages which are not longer needed from the system_, but not purges them, use the --purge option together with the command for that.
> - **apt --purge autoremove** → remove config files and (more important as it cleans dead subdirectories from the documentation tree) entries from /usr/share/doc.
>
> #### Create Aliases

- Ubuntu:

```bash
nano ~/.bashrc
# Append the following text with personal commands. O.S. update in this case:

alias updateos='sudo -- sh -c "sudo apt update && sudo apt -y upgrade && sudo apt -y autoremove"'

# Load changes:
source ~/.bashrc

# List all aliases:
alias
```

- Kali Linux:

```bash
nano ~/.zshrc
# Append the following text with personal commands. O.S. update in this case:

alias updateos='sudo -- sh -c "sudo apt update && sudo apt -y upgrade && sudo apt -y autoremove"'

# Load changes:
source ~/.zshrc

# List all aliases:
alias
```

### Set local timezone

```bash
sudo unlink /etc/localtime
sudo ln -s /usr/share/zoneinfo/Europe/Rome /etc/localtime
sudo timedatectl set-timezone "Europe/Rome"
```

### Set NTP

```bash
sudo apt install -y chrony
# You can configure which time servers you want to use by editing 
# nano /etc/chrony/chrony.conf

sudo systemctl enable --now chrony

sudo systemctl status chrony --no-pager
sudo timedatectl status
# System clock synchronized: yes
# NTP service: active
```

### Set Keyboard layout

```bash
sudo dpkg-reconfigure keyboard-configuration
```

### Disable Password prompt for sudo group

```bash
sudo sed -i.bak 's/%sudo\s\+ALL=(ALL:ALL) ALL/%sudo ALL=(ALL:ALL) NOPASSWD: ALL/' /etc/sudoers
```

### Install packages (deb, etc)

```bash
sudo dpkg -i packname
```

### First settings after O.S. install

```bash
passwd root
passwd kali
sudo apt install -y terminator
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
# make sure all the directives are set to “0”

sudo systemctl disable apt-daily-upgrade.timer
sudo systemctl mask apt-daily-upgrade.service
sudo systemctl disable apt-daily.timer
sudo systemctl mask apt-daily.service

# For complete package uninstall
sudo apt purge --auto-remove unattended-upgrades
```

### Speedtest-cli

```bash
wget -O /dev/null -q --show-progress https://ash-speed.hetzner.com/10GB.bin
```

- Or install `speedtest-cli`

```bash
sudo apt install -y speedtest-cli
```

```bash
speedtest-cli --secure
```

### [Speedtest by Ookla](https://www.speedtest.net/apps/cli)

```bash
sudo apt install -y curl
curl -s https://packagecloud.io/install/repositories/ookla/speedtest-cli/script.deb.sh | sudo bash
sudo apt install -y speedtest
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
sudo apt install -y lm-sensors hddtemp
sudo sensors-detect
/etc/init.d/kmod start
sensors

# Raspberry Pi Temp
vcgencmd measure_temp
```

### [CPU Stress-Terminal UI - s-tui](https://github.com/amanusk/s-tui)

```bash
sudo apt install -y s-tui stress
```

```bash
s-tui

# Output to default CSV file - s-tui_log_<TIME>.csv
s-tui --csv
```

### Renew DHCP

```bash
sudo systemctl restart networking.service

# if doesn't work, release and renew IP with DHCP
sudo dhclient -r
sudo dhclient
```

### [Benchmark](https://bench.sh/)

```bash
# bench.sh
wget -qO- bench.sh | bash
curl -Lso- bench.sh | bash
```

```bash
# DISK Benchmark
hdparm -tT /dev/nvme0n2
```



---

## Software

### Basic Tools

```bash
sudo apt install -y wget net-tools htop tree terminator flameshot
```

### [Install Zsh & Oh-My-Zsh](tools/zsh.md)

### [Install Eza](https://github.com/eza-community/eza/)

```bash
sudo sh -c '
    apt update &&
    apt install -y gpg &&
    mkdir -p /usr/share/keyrings &&
    wget -qO- https://raw.githubusercontent.com/eza-community/eza/main/deb.asc | gpg --dearmor -o /usr/share/keyrings/gierens.gpg &&
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/gierens.gpg] http://deb.gierens.de stable main" | tee /etc/apt/sources.list.d/gierens.list &&
    chmod 644 /usr/share/keyrings/gierens.gpg /etc/apt/sources.list.d/gierens.list &&
    apt update &&
    apt install -y eza
'
```

### Install Sublime

```bash
sudo sh -c '
    wget -qO- https://download.sublimetext.com/sublimehq-pub.gpg | gpg --dearmor -o /usr/share/keyrings/sublimehq-archive.gpg &&
    echo "deb [arch="$(dpkg --print-architecture)" signed-by=/usr/share/keyrings/sublimehq-archive.gpg] https://download.sublimetext.com/ apt/stable/" | tee /etc/apt/sources.list.d/sublime-text.list &&
    apt update &&
    apt install -y sublime-text
'

# APT DEB822 source format
sudo sh -c '
	wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | tee /usr/share/keyrings/sublimehq-pub.asc > /dev/null
	cat <<EOF > /etc/apt/sources.list.d/sublime-text.sources
Types: deb
URIs: https://download.sublimetext.com/
Suites: apt/stable/
Signed-By: /usr/share/keyrings/sublimehq-pub.asc
EOF
	apt update &&
	apt install sublime-text
'
```

### Install VSCode

```bash
sudo apt update && sudo apt install -y software-properties-common apt-transport-https wget

sudo sh -c '
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /usr/share/keyrings/packages.microsoft.gpg > /dev/null
    echo "deb [arch=amd64,arm64,armhf signed-by=//usr/share/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list
    sudo apt update && sudo apt install -y code
'

EXTENSIONS=(
	mhutchie.git-graph \
	esbenp.prettier-vscode \
	redhat.vscode-yaml \
	oderwat.indent-rainbow \
	ms-python.python \
)

for EXT in "${EXTENSIONS[@]}"; do
	code --install-extension "$EXT"
done
```

### Install VS Codium

```bash
wget https://gitlab.com/paulcarroty/vscodium-deb-rpm-repo/raw/master/pub.gpg && sudo mv pub.gpg /usr/share/keyrings/vscodium-archive-keyring.asc

sudo sh -c 'echo "deb [ signed-by=/usr/share/keyrings/vscodium-archive-keyring.asc ] https://paulcarroty.gitlab.io/vscodium-deb-rpm-repo/debs vscodium main" > /etc/apt/sources.list.d/vscodium.list'

sudo apt update && sudo apt install -y codium
```

### Install Obsidian

```bash
# Download Obsidian latest release from https://github.com/obsidianmd/obsidian-releases/releases
mv Obsidian-0.12.4.AppImage usr/local/bin/obsidiann
chmod +x usr/local/bin/obsidian
```

### Install Brave Browser

```bash
sudo sh -c '
    apt install -y curl
    curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg
    echo "deb [arch="$(dpkg --print-architecture)" signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main" | tee /etc/apt/sources.list.d/brave-browser-release.list
    wget http://archive.ubuntu.com/ubuntu/pool/main/libu/libu2f-host/libu2f-udev_1.1.10-3.2_all.deb
    dpkg -i libu2f-udev_1.1.10-3.2_all.deb
    apt update && apt install -y brave-browser
    rm -rf libu2f-udev_1.1.10-3.2_all.deb
'
```

### [Install GitHub Desktop](https://mirror.mwt.me/shiftkey-desktop/)

```bash
wget -qO- "https://mirror.mwt.me/shiftkey-desktop/install.sh" | sudo -s
```

```bash
# or
wget -qO - https://apt.packages.shiftkey.dev/gpg.key | gpg --dearmor | sudo tee /usr/share/keyrings/shiftkey-packages.gpg > /dev/null

sudo sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/shiftkey-packages.gpg] https://apt.packages.shiftkey.dev/ubuntu/ any main" > /etc/apt/sources.list.d/shiftkey-packages.list'

sudo apt update && sudo apt install -y github-desktop
```

### Install Telegram

```bash
# Via apt
sudo apt install -y telegram-desktop
```

```bash
# Via official Linux binary

wget https://telegram.org/dl/desktop/linux
tar -xvf linux
sudo mv Telegram/ /opt
/opt/Telegram/./Telegram
/opt/Telegram/./Updater
```

### Discord

```bash
sudo apt install -y gdebi-core wget

wget -O ~/discord.deb "https://discord.com/api/download?platform=linux&format=deb"

sudo gdebi ~/discord.deb
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
sudo apt install -y anydesk
```

### Install ForticlientVPN

```bash
sudo apt install -y firefox jq lz4json openfortivpn
cd
mkdir tools
cd ~/tools
git clone https://github.com/nonamed01/fuckForticlient.git
cd ~/tools/fuckForticlient

./fuckForticlient.sh -S <SERVER>:<PORT> -c
# Login via browser and retry the command above after logged in
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
sudo apt install -y tor
service tor start
service tor status

# Start browser with proxychain:
proxychains firefox www.duckduckgo.com
	(do not close terminal)

# search for: check dns leaks
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
sudo apt update && sudo apt install -y metasploit-framework
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

### [Install Docker](https://docs.docker.com/engine/install/debian/)

```bash
sudo apt update && sudo apt install -y curl apt-transport-https software-properties-common ca-certificates gnupg

packages=("docker.io" "docker-doc" "docker-compose" "podman-docker" "containerd" "runc")
for pkg in "${packages[@]}"; do
    sudo apt remove "$pkg" -y
done &&

sudo sh -c '
    curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker.gpg &&
    chmod a+r /usr/share/keyrings/docker.gpg &&
    echo "deb [arch="$(dpkg --print-architecture)" signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/debian bullseye stable" |  tee /etc/apt/sources.list.d/docker.list &&
    apt update && 
    apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin &&
    sudo gpasswd -a "${USER}" docker
'
```

### [Install Gophish](https://github.com/gophish/gophish/releases/)

```bash
cd /opt/

# Get the latest version link from https://github.com/gophish/gophish/releases/
sudo wget https://github.com/gophish/gophish/releases/download/v0.12.1/gophish-v0.12.1-linux-64bit.zip

sudo unzip -d gophish gophish-v0.12.1-linux-64bit.zip

sudo chmod +x gophish/gophish

cd /opt/gophish && sudo ./gophish
```

- Setup a [Gophish Demo](https://getgophish.com/blog/post/2019-01-04-creating-the-gophish-demo-part-one/) with a fake campaign

```bash
docker run -ti -p 3333:3333 --rm gophish/demo
```

### [Install Katana](https://github.com/projectdiscovery/katana)

```bash
sudo apt install -y golang

go install github.com/projectdiscovery/katana/cmd/katana@latest

sudo cp ~/go/bin/katana /bin/
```

### Install Asbru CM (Linux Desktop)

- Link [Asbru](https://www.asbru-cm.net/)
- _Ásbrú Connection Manager_ is a user interface that helps organizing remote terminal sessions and automating repetitive tasks.

```bash
curl -1sLf 'https://dl.cloudsmith.io/public/asbru-cm/release/cfg/setup/bash.deb.sh' | sudo -E bash
sudo apt install -y asbru-cm
```

### Install BurpSuite

- Link [BurpSuite Community Download](https://portswigger.net/burp/communitydownload)
  - Use this for updating too

```bash
# Download Burp Suite community edition for Linux 64 bits 
BURP_VER=$(curl -si https://portswigger.net/burp/releases/community/latest | grep -E "^location:" | grep -Eo "[0-9]+.[0-9]+.[0-9]+" | sed 's/-/./g')

wget -qO "/tmp/burpsuite_community_v"$BURP_VER"_install.sh" "https://portswigger.net/burp/releases/startdownload?product=community&version="$BURP_VER"&type=Linux"

chmod +x "/tmp/burpsuite_community_v"$BURP_VER"_install.sh"
sudo /tmp/burpsuite_community_v"$BURP_VER"_install.sh -q

# Default install dir is /opt/BurpSuiteCommunity
# This will overwrite the command of the already installed Burpsuite (with apt)

sudo cp /opt/BurpSuiteCommunity/burpsuite_community.jar /usr/share/burpsuite/burpsuite.jar
sudo mv /usr/bin/burpsuite /usr/bin/burpsuite.bak
echo -ne '#!/bin/sh\n"/opt/BurpSuiteCommunity/BurpSuiteCommunity" %U\n' | sudo tee /usr/bin/burpsuite && sudo chmod +x /usr/bin/burpsuite

# Start burspsuite from shell
```

### [Install MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF)

```bash
sudo mkdir -p ~/docker/mobsf

chown 9901:9901 ~/docker/mobsf

docker run -it --rm --name mobsf -p 8010:8010 -v ~/docker/mobsf:/home/mobsf/.MobSF opensecurity/mobile-security-framework-mobsf:latest
```

### [Install Rustscan](https://github.com/RustScan/RustScan)

```bash
docker run -it --rm --name rustscan rustscan/rustscan:2.1.1

# Create a zsh/bash alias like this
alias rustscan='docker run -it --rm --name rustscan rustscan/rustscan:2.1.1'
```

### [Install CVEMAP](https://github.com/projectdiscovery/cvemap)

```bash
sudo apt install -y golang-go

go install github.com/projectdiscovery/cvemap/cmd/cvemap@latest

# Based on your SHELL, add Go bin to system PATH
echo "export PATH=$PATH:$HOME/go/bin" >> $HOME/.bashrc
echo "export PATH=$PATH:$HOME/go/bin" >> $HOME/.zshrc
echo "export PATH=$PATH:$HOME/go/bin" >> $ZSH_CUSTOM/my_paths.zsh
source $HOME/.bashrc
source $HOME/.zshrc
```

### [Install Rust](https://rustup.rs/)

```bash
sudo apt install -y curl
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

### Install Arch Linux

- [Arch Linux in 12 Minutes - Chris Titus](https://www.youtube.com/watch?v=PqGnlEmfYjM)

```bash
# Create VM with Arch ISO - https://archlinux.org/download/

# Proceed with https://github.com/ChrisTitusTech/linutil
curl -fsSL https://christitus.com/linux | sh
# System Setup -> Arch Linux -> Arch Server Setup

yay -S fastfetch

# Install GUI
curl -fsSL https://christitus.com/linux | sh
# System Setup -> Arch Linux -> Yay AUR Helper
# Application Setup -> v and multi select:
# Alacritty
# Bash Prompt
# DWM-Titus (custom window manager)
# Rofi
reboot # and login
yay -S firefox
```



---

## Usage and Configuration

### SSH Login

```bash
sudo systemctl enable ssh --now
```

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

git clone git@github.com:syselement/REPOSITORY.git
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

### [Global Socket](https://www.gsocket.io/)

> *Global Socket allows two workstations on different private networks to communicate with each other. Through firewalls and through NAT - like there is no firewall.*

```bash
# Install
bash -c "$(curl -fsSL https://gsocket.io/y)"

# Uninstall
GS_UNDO=1 bash -c "$(curl -fsSL https://gsocket.io/y)"

# Access (from another place)
S="ExampleSecretChangeMe" bash -c "$(curl -fsSL https://gsocket.io/y)"
```

### nmap

```bash
nmap 192.168.254.129
nmap 192.168.254.1/24
nmap 192.168.254.1-100
nmap -p80,21,23 192.168.254.129
```

### RDP with Xfce (Kali Linux)

> 🔗 [Setting up RDP with Xfce - Kali Linux](https://www.kali.org/docs/general-use/xfce-with-rdp/)

```bash
wget https://gitlab.com/kalilinux/recipes/kali-scripts/-/raw/main/xfce4.sh
chmod +x xfce4.sh
sudo ./xfce4.sh

sudo adduser xrdp ssl-cert
sudo systemctl enable xrdp --now
```

> - The **`xfce4.sh`** does the following
>
> ```bash
> #!/bin/bash
> echo "[i] Updating and upgrading Kali (this will take a while)"
> apt-get update
> apt-get --yes --force-yes dist-upgrade
>
> echo "[i] Installing Xfce4 & xrdp (this will take a while as well)"
> apt-get --yes --force-yes install kali-desktop-xfce xorg xrdp
>
> echo "[i] Configuring xrdp to listen to port 3390 (but not starting the service)"
> sed -i 's/port=3389/port=3390/g' /etc/xrdp/xrdp.ini
> ```

- Fix for `Authentication Required to Create Managed Color Device`, run

```bash
cat <<EOF | sudo tee /etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla
[Allow Colord all Users]
Identity=unix-user:*
Action=org.freedesktop.color-manager.create-device;org.freedesktop.color-manager.create-profile;org.freedesktop.color-manager.delete-device;org.freedesktop.color-manager.delete-profile;org.freedesktop.color-manager.modify-device;org.freedesktop.color-manager.modify-profile
ResultAny=no
ResultInactive=no
ResultActive=yes
EOF

sudo systemctl restart xrdp
```

- Port to connect to is `3390`

```bash
### TESTS - DO NOT CONSIDER ###

# sudo nano /etc/xrdp/startwm.sh

# # Add lines before test and execute
# unset DBUS_SESSION_BUS_ADDRESS
# unset XDG_RUNTIME_DIR
# . $HOME/.profile
```

### Arm on x86 QEMU-USER

> 🔗 [Running arm binaries on x86 with qemu-user](https://azeria-labs.com/arm-on-x86-qemu-user/)

```bash
sudo apt update -y && sudo apt upgrade -y
sudo apt install -y qemu-user qemu-user-static gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu binutils-aarch64-linux-gnu-dbg build-essential libc6-dev-arm64-cross

qemu-aarch64-static -L /usr/aarch64-linux-gnu/ sandbox
```

### Kali repos on Debian/Ubuntu apt

```bash
#!/bin/bash

# Add kali repository to apt sources
echo "[i] Adding Kali Linux repository to apt sources"
sudo apt install -y gnupg

# Download the Kali archive key, dearmor it, and save it to the keyring
sudo sh -c 'wget -qO - https://archive.kali.org/archive-key.asc | gpg --dearmor | tee /usr/share/keyrings/kali-archive-keyring.gpg > /dev/null'

# Add Kali repository entry to sources list
sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/kali-archive-keyring.gpg] https://http.kali.org/kali kali-rolling main non-free contrib" | sudo tee /etc/apt/sources.list.d/kali.list'

# Set low priority for Kali repository
echo "[i] Setting low priority for Kali repository"
sudo touch /etc/apt/preferences.d/kali.pref
sudo chmod 666 /etc/apt/preferences.d/kali.pref
echo 'Package: *' > /etc/apt/preferences.d/kali.pref
echo 'Pin: release a=kali-rolling' >> /etc/apt/preferences.d/kali.pref
echo 'Pin-Priority: 50' >> /etc/apt/preferences.d/kali.pref
sudo chmod 644 /etc/apt/preferences.d/kali.pref

# Update package lists
sudo apt update

echo "[i] Install completed."
```



---

## Virtual Machines

### Install VirtualBox on Kali

> 🔗 [Install VirtualBox on Kali (Host)](https://www.kali.org/docs/virtualization/install-virtualbox-host/)

```bash
sudo apt update

curl -fsSL https://www.virtualbox.org/download/oracle_vbox_2016.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/oracle_vbox_2016.gpg

curl -fsSL https://www.virtualbox.org/download/oracle_vbox.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/oracle_vbox.gpg

echo "deb [arch=amd64] https://download.virtualbox.org/virtualbox/debian bullseye contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list

sudo apt update

sudo apt install -y dkms

sudo apt install -y virtualbox virtualbox-ext-packv
```

### VirtManager backup qcow2 VM

```bash
# Backup VMs

sudo -s
# Now that the terminal is using root, run the virsh tool to create a backup of the VM definitions file.

echo '' > nameofvm.xml
virsh dumpxml nameofvm >  /var/lib/libvirt/images/nameofvm.xml
# Use ls to find the name of the Qcow2 virtual hard drive in /var/lib/libvirt/images/. Copy the file name, and use mv to move it into a new sub-directory.

mkdir -p vm-backup
cd vm-backup
mv example.qcow2 vm-backup

mv nameofvm.xml vm-backup


# Using tar, create an archive of the VM disk and XML file.
tar jcvfp my-vm-backup.tar.bz2 vm-backup

mv my-vm-backup.tar.bz2 /home/username/
# Backing up VM disk images takes a long time. Depending on the file size, it could take an hour or more. Best to just let the PC do its thing. When the backup is complete, feel free to move my-vm-backup.tar.bz2 to another server, cloud backup and etc.


# Restoring backups
To restore a backup, you’ll first need to extract it.

tar xvfp my-vm-backup.tar.bz2
cd vm-backup

# Use virsh to restore the XML file.
sudo -s
virsh define --file /home/username/location/of/exctracted/archive/vm-backup/nameofvm.xml

# Lastly, move the disk image to the images directory.
mv example.qcow2 /var/lib/libvirt/images/
```

### Metaspoitable VM

> 📌 Check **Metasploitable3** VM [here](../../home-lab/redteam/metasploitable3.md)
>
> - [Metasploitable2 - Download link](https://sourceforge.net/projects/metasploitable/files/Metasploitable2/)
> - Guides:
>   - [Metasploitable 2](https://docs.rapid7.com/metasploit/metasploitable-2/)
>   - [Metasploitable 2 Exploitability Guide](https://docs.rapid7.com/metasploit/metasploitable-2-exploitability-guide/)

```bash
# Login: msfadmin:msfadmin
```

### Root Servers Shells

> - [Free Linux Cloud Root Shells](https://iq.thc.org/free-linux-cloud-root-shells)
> - [Segfault | The Hacker’s Choice](https://www.thc.org/segfault/)

```bash
ssh root@segfault.net
# The password is 'segfault'
```



---

## Troubleshooting

### Disable a specific bluetooth adapter

```bash
hciconfig -a

hci1:   Type: Primary  Bus: USB
    BD Address: 5C:F3:70:77:17:42  ACL MTU: 1021:8  SCO MTU: 64:1
    UP RUNNING 
    RX bytes:41905 acl:130 sco:0 events:5165 errors:0
    TX bytes:5896886 acl:9645 sco:0 commands:304 errors:0
    Features: 0xbf 0xfe 0xcf 0xfe 0xdb 0xff 0x7b 0x87
    Packet type: DM1 DM3 DM5 DH1 DH3 DH5 HV1 HV2 HV3 
    Link policy: RSWITCH SNIFF 
    Link mode: PERIPHERAL ACCEPT 
    Name: 'asus-bt-usb'
    Class: 0x7c010c
    Service Classes: Rendering, Capturing, Object Transfer, Audio, Telephony
    Device Class: Computer, Laptop
    HCI Version: 4.0 (0x6)  Revision: 0x1000
    LMP Version: 4.0 (0x6)  Subversion: 0x220e
    Manufacturer: Broadcom Corporation (15)

hci0:   Type: Primary  Bus: USB
    BD Address: 5C:51:4F:13:7E:22  ACL MTU: 1021:5  SCO MTU: 96:5
    UP RUNNING 
    RX bytes:68635 acl:301 sco:0 events:8237 errors:0
    TX bytes:4500654 acl:7333 sco:0 commands:849 errors:0
    Features: 0xff 0xfe 0x0f 0xfe 0xdb 0xff 0x7b 0x87
    Packet type: DM1 DM3 DM5 DH1 DH3 DH5 HV1 HV2 HV3 
    Link policy: RSWITCH HOLD SNIFF 
    Link mode: PERIPHERAL ACCEPT 
    Name: 'asus-internal-bt'
    Class: 0x7c010c
    Service Classes: Rendering, Capturing, Object Transfer, Audio, Telephony
    Device Class: Computer, Laptop
    HCI Version: 4.0 (0x6)  Revision: 0x500
    LMP Version: 4.0 (0x6)  Subversion: 0x500
    Manufacturer: Intel Corp. (2)
```

```bash
sudo nano /etc/init.d/disable_builtin_bluetooth

#!/bin/bash
echo "Disabling hci0 bluetooth adapter"
/usr/sbin/hciconfig hci0 down &

sudo chmod +x /etc/init.d/disable_builtin_bluetooth

update-rc.d disable_builtin_bluetooth start 26 2 3 4 5  .

sudo systemctl enable bluetooth 

reboot
```

---

### Audio/Microphone Bluetooth Fix with HFP/HSP (Headset Head Unit)

```bash
sudo apt-get update
sudo apt install -y pipewire libspa-0.2-bluetooth pipewire-audio-client-libraries
systemctl --user daemon-reload
systemctl --user --now disable pulseaudio.service pulseaudio.socket
systemctl --user --now enable pipewire pipewire-pulse
systemctl --user mask pulseaudio

sudo systemctl --user restart pipewire-pulse
pactl info
```

```bash
# Parrot OS
sudo systemctl list-unit-files --type=service
sudo systemctl disable blueman-mechanism.service
sudo systemctl disable bluetooth.service
sudo apt install -y libspa-0.2-modules=0.3.19-4
sudo apt install -y libspa-0.2-bluetooth
sudo apt purge -y pulseaudio-module-bluetooth
sudo apt install -y pulseaudio-module-bluetooth
sudo systemctl enable --now bluetooth.service
sudo reboot

sudo systemctl enable --now blueman-mechanism.service
sudo systemctl enable --now bluetooth.service
```

```bash
sudo systemctl list-unit-files --type=service | grep blue

blueman-mechanism.service enabled         enabled
bluetooth.service	enabled         enabled
dbus-org.bluez.service alias           -
```

---

### Debug and optimize long boot

```bash
systemd-analyze && systemd-analyze blame | head -n 15
systemd-analyze critical-chain
# This shows exactly which services are slowing the boot (ordered by delay)

# Disable or Mask unneeded services
# e.g.
sudo systemctl disable --now plocate-updatedb.timer locate.timer exim4-base.timer && \
sudo systemctl mask plocate-updatedb.timer locate.timer exim4-base.timer && \
sudo systemctl disable --now plocate-updatedb.service locate.service exim4-base.service && \
sudo systemctl mask plocate-updatedb.service locate.service exim4-base.service

# Reboot and re-analyze
sudo reboot
systemd-analyze && systemd-analyze blame | head -n 15
systemd-analyze critical-chain
sudo dmesg --ctime --level=err,warn
```

---

### Disable NetworkManager-wait-online.service - script

```bash
nano nm-wait-toggle.sh
```

```bash
#!/bin/bash

SERVICE="NetworkManager-wait-online.service"
STATUS_FILE="$HOME/${SERVICE}.status"
ACTIVE_FILE="$HOME/${SERVICE}.active"

case "$1" in
  backup)
    systemctl is-enabled $SERVICE > "$STATUS_FILE"
    systemctl is-active $SERVICE > "$ACTIVE_FILE"
    echo "[✔] Backed up state to:"
    echo "    $STATUS_FILE"
    echo "    $ACTIVE_FILE"
    ;;
    
  disable)
    sudo systemctl disable --now $SERVICE
    sudo systemctl mask $SERVICE
    echo "[✔] Disabled and masked $SERVICE"
    ;;

  restore)
    if [ -f "$STATUS_FILE" ] && [ -f "$ACTIVE_FILE" ]; then
      sudo systemctl unmask $SERVICE
      if grep -q enabled "$STATUS_FILE"; then
        sudo systemctl enable $SERVICE
      else
        sudo systemctl disable $SERVICE
      fi

      if grep -q active "$ACTIVE_FILE"; then
        sudo systemctl start $SERVICE
      fi
      echo "[✔] Restored $SERVICE to previous state"
    else
      echo "[✖] Backup files not found. Run '$0 backup' first."
    fi
    ;;

  *)
    echo "Usage: $0 {backup|disable|restore}"
    exit 1
    ;;
esac
```

```bash
# Usage
chmod +x nm-wait-toggle.sh

bash nm-wait-toggle.sh backup     # Save current service state
bash nm-wait-toggle.sh disable    # Disable and mask it for faster boot
bash nm-wait-toggle.sh restore    # Restore original state if needed
```



---

## Logs

```bash
ls -lah /var/log/

auth.log
boot.log
cron.log
httpd
dpkg.log
kern.log
syslog
user.log
...

/var/log/apache2/access.log
/var/log/apache2/error.log
```

---

