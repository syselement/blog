# 📃 Everything Linux

![](.gitbook/assets/Linux-logo-small.png)

> 📜 In this 🐧 **Linux** notes repository, I store all of my notes related to the Linux operating system and Unix based apps.
>
> * 📌_Some commands can be outdated._
> * ❗_Most of the commands are for **Debian-based** distributions._

***

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

* To enable swap with `btrfs` disk run

```bash
sudo btrfs filesystem mkswapfile --size 4G /swapfile
sudo swapon /swapfile
```

### Disk - Extend LVM Partition

```bash
echo 1>/sys/class/block/sda/device/rescan
sudo cfdisk
# Resize the wanted partition
# sudo lsblk
# sudo vgdisplay -v
# sudo pvresize /dev/<your_sda>
# sudo lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
# sudo vgdisplay -v
# sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
df -h
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
> * **apt clean** → cleans the packages and install script in /var/cache/apt/archives/ (_removes all stored archives in your cache_)
> * **apt autoclean** → cleans obsolete deb-packages, _**less than clean**_ (_removes all stored archives in your cache for packages that can not be downloaded anymore_ (thus packages that are no longer in the repo or that have a newer version in the repo))
> * **apt autoremove** → _removes orphaned packages which are not longer needed from the system_, but not purges them, use the --purge option together with the command for that.
> * **apt --purge autoremove** → remove config files and (more important as it cleans dead subdirectories from the documentation tree) entries from /usr/share/doc.
>
> #### Create Aliases

* Ubuntu:

```bash
nano ~/.bashrc
# Append the following text with personal commands. O.S. update in this case:

alias updateos='sudo -- sh -c "sudo apt update && sudo apt -y upgrade && sudo apt -y autoremove"'

# Load changes:
source ~/.bashrc

# List all aliases:
alias
```

* Kali Linux:

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

### Install packages (deb, etc)

```bash
sudo dpkg -i packname
```

### First settings after O.S. install

```bash
passwd root
passwd kali
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

* Or install `speedtest-cli`

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

### [CPU Stress-Terminal UI - s-tui](https://github.com/amanusk/s-tui)

```bash
sudo apt install s-tui stress
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

***

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
```

### Install VSCode

```bash
sudo apt install -y software-properties-common apt-transport-https wget

wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /usr/share/keyrings/packages.microsoft.gpg > /dev/null
 
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=//usr/share/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'

sudo apt update && sudo apt install -y code
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
    apt update && apt install -y brave-browser
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

sudo apt update && sudo apt install github-desktop
```

### Install Telegram

```bash
# Via apt
sudo apt install telegram-desktop
```

```bash
# Via official Linux binary

wget https://telegram.org/dl/desktop/linux
tar -xvf linux
sudo mv Telegram/ /opt
/opt/Telegram/./Telegram
/opt/Telegram/./Updater
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
sudo apt install firefox jq lz4json openfortivpn
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

* Or use APT package manager on Debian-based distros

```bash
sudo apt update && sudo apt install -y metasploit-framework
```

* Enable `postgresql` at boot, start the service and initialize MSF database

```bash
sudo systemctl enable postgresql
sudo systemctl restart postgresql
sudo msfdb init
```

* Run **`msfconsole`** to start the Metasploit Framework Console

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

* Setup a [Gophish Demo](https://getgophish.com/blog/post/2019-01-04-creating-the-gophish-demo-part-one/) with a fake campaign

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

* Link [Asbru](https://www.asbru-cm.net/)
* _Ásbrú Connection Manager_ is a user interface that helps organizing remote terminal sessions and automating repetitive tasks.

```bash
curl -1sLf 'https://dl.cloudsmith.io/public/asbru-cm/release/cfg/setup/bash.deb.sh' | sudo -E bash
sudo apt install -y asbru-cm
```

### Install BurpSuite

* Link [BurpSuite Community Download](https://portswigger.net/burp/communitydownload)
  * Use this for updating too

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

***

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

### NMAP

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

> * The **`xfce4.sh`** does the following
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

* Fix for `Authentication Required to Create Managed Color Device`, run

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

* Port to connect to is `3390`

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
sudo apt install qemu-user qemu-user-static gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu binutils-aarch64-linux-gnu-dbg build-essential libc6-dev-arm64-cross

qemu-aarch64-static -L /usr/aarch64-linux-gnu/ sandbox
```

***

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

### METASPLOITABLE VM

> 📌 Check **Metasploitable3** VM [here](../home-lab/redteam/metasploitable3.md)

* [Metasploitable2 - Download link](https://sourceforge.net/projects/metasploitable/files/Metasploitable2/)
* Guides:
  * [Metasploitable 2](https://docs.rapid7.com/metasploit/metasploitable-2/)
  * [Metasploitable 2 Exploitability Guide](https://docs.rapid7.com/metasploit/metasploitable-2-exploitability-guide/)

```bash
# Login: msfadmin:msfadmin
```

***

## Troubleshooting

### i915 Linux Freeze - Temp Fix

> * 🔗 [flip\_done timed out - issue](https://gitlab.freedesktop.org/drm/intel/-/issues/8685)
>
> Happens on Kali and Parrot OS with HDMI external monitor and Iris Xe Graphics.

```bash
sudo cat /sys/kernel/debug/dri/0/i915_dmc_info

# Verifica la versione e annotarsela

ls -lah /usr/lib/firmware/i915/adlp_dmc*

# Rinominare i file per rendere la versione utilizzata la 2.16 tramite il file adlp_dmc.bin

sudo mv /usr/lib/firmware/i915/adlp_dmc.bin /usr/lib/firmware/i915/adlp_dmc_ver<VERSIONE_PRIMO_COMANDO>.bin
sudo cp /usr/lib/firmware/i915/adlp_dmc_ver2_16.bin /usr/lib/firmware/i915/adlp_dmc.bin
sudo update-initramfs -c -k all

# Verificare la version utilizzata (può essere necessario un reboot)
sudo cat /sys/kernel/debug/dri/0/i915_dmc_info
```
