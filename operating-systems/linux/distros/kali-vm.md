# Kali Linux - VM

![](.gitbook/assets/kali.svg) [Kali Linux](https://www.kali.org/)

---

## 🌐 Resources 🔗

> - [Kali docs](https://www.kali.org/docs/)
> - [kali-meta | Kali Linux Tools](https://www.kali.org/tools/kali-meta/)

---

## [First Install - Virtual Machine](https://www.kali.org/docs/installation/hard-disk-install/)

- Create a new Virtual Machine in VMWare with preferred specs:
  - Processors: `1 CPU - 2 Cores`
  - RAM Memory: `8 GB`
  - Hard Disk: `60 GB`
  - CD: use the Kali Installer .iso image file - [Download here](https://www.kali.org/get-kali/) - and check *Connect at power on*
  - Network: set it as *NAT*
- Boot the virtual machine and proceed with the [Kali Installation](https://www.kali.org/docs/installation/hard-disk-install/)

![](.gitbook/assets/image-20230307160636827.png)

---

## First Boot & [Update](https://www.kali.org/docs/general-use/updating-kali/)

- Boot Kali Linux
- Open the Terminal and run the command

```bash
sudo apt update && sudo apt full-upgrade -y && sudo apt -y autoremove
```

> - **`full-upgrade`** performs the function of upgrade but will remove currently installed packages if this is needed to upgrade the system as a whole.
> - This command will upgrade the system to the latest Kali version.
> - Tips from the [Updating Kali](https://www.kali.org/docs/general-use/updating-kali/) post:
>   - ❗ *You should be checking for updates every few weeks*
>   - 📌 *A good practice is that you ensure **all tools work before an engagement** and during that engagement you should not update*

- Reboot the system

```bash
reboot
```

- Customize Kali and finish off the setup using `kali-tweaks` if necessary

```bash
kali-tweaks
```

![kali-tweaks](.gitbook/assets/image-20230307161431755.png)

---

## Configurations

### Basic config

```bash
# Timezone for "Europe/Rome"
sudo unlink /etc/localtime
sudo ln -s /usr/share/zoneinfo/Europe/Rome /etc/localtime
sudo timedatectl set-timezone "Europe/Rome"

# Change "root" user password
sudo passwd root

# Disable Password prompt for sudo group
sudo sed -i.bak 's/%sudo\s\+ALL=(ALL:ALL) ALL/%sudo ALL=(ALL:ALL) NOPASSWD: ALL/' /etc/sudoers

# Set GRUB Timeout
sudo sed -E '/^GRUB_TIMEOUT=/s/=(.*)/=1/' -i /etc/default/grub
sudo update-grub
```

### SSH keys

- **Host SSH keys** - resets the host's SSH identity and **enables SSH** (disable if not necessary)
- **User SSH key pair** - creates a new personal SSH login key
- **Add to ssh-agent** - loads the key for automatic use

```bash
# Host ssh keys
sudo /bin/rm -v /etc/ssh/ssh_host_*
sudo dpkg-reconfigure openssh-server
sudo systemctl enable --now ssh

# User ssh key pair
cd
mkdir -p $HOME/.ssh
cd $HOME/.ssh
ssh-keygen -t ed25519
# Type a secure passphrase when asked
chmod 700 $HOME/.ssh
chmod 600 $HOME/.ssh/*

# Add the SSH private key to the ssh-agent
eval "$(ssh-agent -s)" && ssh-add $HOME/.ssh/id_ed25519
```

---

### Install [JetBrainsMono Nerd Font](https://www.nerdfonts.com/font-downloads)

```bash
# Install etBrainsMono Nerd Font
cd
mkdir -p $HOME/.local/share/fonts
cd $HOME/.local/share/fonts
curl -fLO https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip
unzip JetBrainsMono.zip
rm JetBrainsMono.zip

fc-cache -fv
```

### [Terminator](https://github.com/gnome-terminator/terminator)

```bash
sudo apt install -y terminator
```

- Config file -> `$HOME/.config/terminator/config`
  - Make sure to have already installed the necessary font

```bash
rm -f "$HOME/.config/terminator/config"
mkdir -p "$HOME/.config/terminator" && touch "$HOME/.config/terminator/config"

# Basic layout with maximized windows, custom font, infinite scrollback, no transparency

cat > "$HOME/.config/terminator/config" << 'EOF'
[global_config]
  window_state = maximise
[keybindings]
[profiles]
  [[default]]
    font = JetBrainsMono Nerd Font Mono 16
    foreground_color = "#f6f5f4"
    show_titlebar = False
    scrollback_infinite = True
    disable_mousewheel_zoom = True
    use_system_font = False
[layouts]
  [[default]]
    [[[window0]]]
      type = Window
      parent = ""
    [[[child1]]]
      type = Terminal
      parent = window0
[plugins]
EOF
```

- From the `Default Applications / Utilities` menu, set `terminator` as the default terminal to use

![](.gitbook/assets/image-20230307153127021.png)

```bash
sudo update-alternatives --config x-terminal-emulator
```

![](.gitbook/assets/image-20230307152343811.png)

- [Terminator Shortcuts here](../tools/Terminator-Shortcuts.md)
- ⌨️ `CTRL+ALT+T` is a keyboard shortcut already configured in Kali. It should open the `terminator` with your configured layout.

### Zsh & Oh-My-Zsh

> Follow the guide here to setup `ZSH` with `Oh-My-Zsh` - [Zsh & Oh-My-Zsh - syselement](../tools/zsh.md) and some useful aliases

---

## Personal Kali Config

```bash
# KALI Linux XFCE Theme mod
echo -ne "\n--------Kali Theme Mod--------\n"

## Login: Kali-Dark + Wallpaper + User
sudo apt install kali-wallpapers-2020.4
sudo ln -sf /usr/share/backgrounds/kali/kali-neon-16x9.png /usr/share/desktop-base/kali-theme/login/background
sudo sed s:"Kali-Light":"Kali-Dark":g -i /etc/lightdm/lightdm-gtk-greeter.conf
sudo sed '/#greeter-hide-users=false/s/^#//g' -i /etc/lightdm/lightdm.conf

## Desktop Wallpaper
xfconf-query -c xfce4-desktop -p /backdrop/screen0/monitorVirtual1/workspace0/last-image --create -t string -s /usr/share/backgrounds/kali/kali-neon-16x9.png

## Show Panel on primary display
xfconf-query -c xfce4-panel -p /panels/panel-1/output-name --create -t string -s "Primary" 

## Turn Numlock ON
xfconf-query -c keyboards -p /Default/Numlock --create -t bool -s true 

## Disable Display sleep and Disable Screensaver
xfconf-query -c xfce4-screensaver -p /lock/enabled -n -t bool -s true
xfconf-query -c xfce4-screensaver -p /lock/saver-activation/enabled -n -t bool -s true
xfconf-query -c xfce4-screensaver -p /saver/enabled -n -t bool -s false
xfconf-query -c xfce4-screensaver -p /saver/fullscreen-inhibit -n -t bool -s true
xfconf-query -c xfce4-screensaver -p /saver/idle-activation/enabled -n -t bool -s false
xfconf-query -c xfce4-screensaver -p /saver/mode -n -t int -s 0
xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/dpms-enabled -n -t bool -s false
xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/dpms-on-ac-off -n -t int -s 0
xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/dpms-on-ac-sleep -n -t int -s 0
xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/general-notification -n -t bool -s true
xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/lock-screen-suspend-hibernate -n -t bool -s true
xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/power-button-action -n -t int -s 3
xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/show-panel-label -n -t int -s 0
xfconf-query -c xfce4-power-manager -p /xfce4-power-manager/show-tray-icon -n -t bool -s false
```

```bash
## Set Keybindings - Print, Alt+Shift+S, Win+E, Alt+T ##
      
cp "$HOME/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-keyboard-shortcuts.xml" "$HOME/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-keyboard-shortcuts.xml.bak"

# Insert the following lines under the first <property name="custom" type="empty">
awk '
    found == 0 && /<property name="custom" type="empty">/ {
        print;
        print "      <property name=\"&lt;Alt&gt;t\" type=\"string\" value=\"terminator\"/>";
        print "      <property name=\"&lt;Shift&gt;&lt;Alt&gt;s\" type=\"string\" value=\"/usr/bin/flameshot gui --clipboard\"/>";
        print "      <property name=\"Print\" type=\"string\" value=\"/usr/bin/flameshot gui --clipboard\"/>";
        found = 1;
        next;
    }
    { print }
' "$HOME/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-keyboard-shortcuts.xml" > /tmp/xfce4-keyboard-shortcuts.xml && \
mv /tmp/xfce4-keyboard-shortcuts.xml "$HOME/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-keyboard-shortcuts.xml"
```

```bash
## Panel configuration
# to git pull from Github repo and import into $HOME/.config/xfce4/panel/
```

```bash
## Set autologin for <username> ##
# sudo nano /etc/lightdm/lightdm.conf
# # In the [Seat:*] section of the file, set the following values:
# autologin-user=<username>
# autologin-user-timeout=0

# Set Adwaita-dark Theme
# xfconf-query -c xsettings -p /Net/ThemeName -s "Adwaita-dark"
```



### CTF Configs

- Download and add hacking platforms `.ovpn` files
- To setup OpenVpn aliases check the **Zsh & Oh-My-Zsh** section

```bash
mkdir -p "$HOME"/htb "$HOME"/tcm "$HOME"/pwnx "$HOME"/tools
# Copy every .ovpn file in the respective dir
# e.g. File names
# htb.ovpn
# htbvip.ovpn
# thm.ovpn
# pwnx.ovpn
```

```bash
sudo sh -c 'gunzip -c /usr/share/wordlists/rockyou.txt.gz > /usr/share/wordlists/rockyou.txt'
```



---

## Tools

### Basic Tools

- Install basic tools

```bash
sudo apt install -y apt-transport-https btop curl duf eza fastfetch flameshot gdu git-all htop locate nano net-tools npm pipx software-properties-common speedtest-cli sshpass terminator tmux tor tree ugrep vlc wget xclip
```

### [Sublime](https://www.sublimetext.com/docs/linux_repositories.html)

```bash
sudo sh -c '
    wget -qO- https://download.sublimetext.com/sublimehq-pub.gpg | gpg --dearmor -o /usr/share/keyrings/sublimehq-archive.gpg &&
    echo "deb [arch="$(dpkg --print-architecture)" signed-by=/usr/share/keyrings/sublimehq-archive.gpg] https://download.sublimetext.com/ apt/stable/" | tee /etc/apt/sources.list.d/sublime-text.list &&
    apt update &&
    apt install -y sublime-text
'
```

```bash
# APT DEB822 source format
sudo sh -c '
	wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | tee /usr/share/keyrings/sublimehq-pub.asc > /dev/null &&
	cat <<EOF > /etc/apt/sources.list.d/sublime-text.sources
Types: deb
URIs: https://download.sublimetext.com/
Suites: apt/stable/
Signed-By: /usr/share/keyrings/sublimehq-pub.asc
EOF &&
	apt update &&
	apt install sublime-text
'
```

- Run it with **`subl`** command.

### [Brave](https://brave.com/linux/)

```bash
sudo sh -c '
    apt install -y curl &&
    curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg &&
    echo "deb [arch="$(dpkg --print-architecture)" signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main" | tee /etc/apt/sources.list.d/brave-browser-release.list &&
    wget http://archive.ubuntu.com/ubuntu/pool/main/libu/libu2f-host/libu2f-udev_1.1.10-3.2_all.deb &&
    dpkg -i libu2f-udev_1.1.10-3.2_all.deb &&
    apt update &&
    apt install -y brave-browser &&
    rm -rf libu2f-udev_1.1.10-3.2_all.deb
'
```

### [Github Desktop](https://github.com/shiftkey/desktop)

```bash
sudo sh -c '
	wget -qO - https://apt.packages.shiftkey.dev/gpg.key | gpg --dearmor | tee /usr/share/keyrings/shiftkey-packages.gpg > /dev/null &&
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/shiftkey-packages.gpg] https://apt.packages.shiftkey.dev/ubuntu/ any main" > /etc/apt/sources.list.d/shiftkey-packages.list &&
    apt update &&
    apt install -y github-desktop
'
```

### [VS Codium](https://github.com/VSCodium/vscodium)

```bash
sudo sh -c '
    curl -fsSLo /usr/share/keyrings/vscodium-archive-keyring.asc https://gitlab.com/paulcarroty/vscodium-deb-rpm-repo/raw/master/pub.gpg &&
	echo "deb [ signed-by=/usr/share/keyrings/vscodium-archive-keyring.asc ] https://paulcarroty.gitlab.io/vscodium-deb-rpm-repo/debs vscodium main" > /etc/apt/sources.list.d/vscodium.list &&
	apt update &&
	apt install -y codium
'
```

### [Docker](https://www.kali.org/docs/containers/installing-docker-on-kali/)

```bash
# Install Docker Engine via APT repository

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
    apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
'

# Add the current user to the "docker" group to let it run Docker
sudo groupadd docker
sudo gpasswd -a "${USER}" docker

# Enable the services at boot
# sudo systemctl enable --now docker.service containerd.service

# OR Disable the services at boot
sudo systemctl disable docker.service containerd.service
# still has docker.socket active to start the Docker service only when necessary

# Reboot and Test
reboot
docker run hello-world
```

### [Flameshot](https://flameshot.org/docs/guide/wayland-help/#gnome-shortcut-does-not-trigger-flameshot)

```bash
sudo apt install flameshot
```

- Config file -> `$HOME/.config/flameshot/flameshot.ini`
  - Make sure to have already installed the necessary font

```bash
mkdir -p "$HOME/.config/flameshot/" && touch "$HOME/.config/flameshot/flameshot.ini"

cat > "$HOME/.config/flameshot/flameshot.ini" << 'EOF'
[General]
contrastOpacity=188
copyPathAfterSave=false
saveAfterCopy=true
saveAsFileExtension=png
saveLastRegion=true
savePath=/home/syselement/Pictures/flameshot
savePathFixed=true
showHelp=false
showMagnifier=false
showStartupLaunchMessage=false
squareMagnifier=true
startupLaunch=true
EOF
```

-  Set this as a custom **Keyboard/Application shortcut** for `flameshot`
   -  I use `Shift+Alt+S` (configured in the **Personal Kali Config** above)


---

## Security Tools

> - [Active directory pentesting: cheatsheet and beginner guide - HTB](https://www.hackthebox.com/blog/active-directory-penetration-testing-cheatsheet-and-guide)

### [AutoRecon](https://github.com/Tib3rius/AutoRecon)

> *AutoRecon is a multi-threaded network reconnaissance tool which performs automated enumeration of services. It is intended as a time-saving tool for use in CTFs and other penetration testing environments (e.g. OSCP). It may also be useful in real-world engagements.*
>
> 
>
> 📌 Installs dependencies:
>
> - dirsearch
> - enum4linux-ng
> - feroxbuster
> - oscanner
> - python3-ntlm-auth
> - python3-requests-ntlm
> - python3-toml
> - seclists
> - sipvicious
> - tnscmd10g

```bash
sudo apt update -y && sudo apt install -y autorecon

mkdir -p $HOME/scans/autorecon
```

```bash
cd $HOME/scans/autorecon
sudo autorecon <TARGET-IP>
```

```bash
# ./results/target Directory Structure
.
├── exploit/
├── loot/
├── report/
│   ├── local.txt
│   ├── notes.txt
│   ├── proof.txt
│   └── screenshots/
└── scans/
	├── _commands.log
	├── _manual_commands.txt
	├── tcp80/
	├── udp53/
	└── xml/
```

### [BloodHound CE](https://github.com/SpecterOps/BloodHound)

> BloodHound - uses graph theory to reveal the hidden and often unintended relationships within an Active Directory or Azure environment
>
> - [BloodHound Community Edition Quickstart - SpecterOps](https://bloodhound.specterops.io/get-started/quickstart/community-edition-quickstart)

```bash
# Requirement: Install Docker (see above)

sudo sh -c '
	apt update -y
	mkdir -p /opt/bloodhound
	cd /opt/bloodhound
	wget https://github.com/SpecterOps/bloodhound-cli/releases/latest/download/bloodhound-cli-linux-amd64.tar.gz
	tar -xvzf bloodhound-cli-linux-amd64.tar.gz
	ln -s /opt/bloodhound/bloodhound-cli /usr/local/bin/bloodhound-cli
	/opt/bloodhound/bloodhound-cli install
'

# Start
sudo bloodhound-cli containers start

# Stop
sudo bloodhound-cli containers stop
```

- Go to [http://localhost:8080/ui/login](http://localhost:8080/ui/login), and log in with `admin` and the randomly generated password from the last installation step. Reset the password as prompted.
- To get data into BloodHound, ingest sample data or run a data collector.

```bash
# Other commands
sudo bloodhound-cli resetpwd
sudo bloodhound-cli update
sudo bloodhound-cli config get default_password
```

### [BloodHound.py CE](https://github.com/dirkjanm/BloodHound.py)

- Install by cloning this repository `git clone https://github.com/dirkjanm/BloodHound.py`, checking out the CE branch `git checkout bloodhound-ce` and running `pip install .` from the project directory.

- The **BloodHound.py CE ingestor** will add a command line tool `bloodhound-ce-python` to your PATH.

```bash
mkdir -p $HOME/tools
cd $HOME/tools
git clone https://github.com/dirkjanm/BloodHound.py

cd BloodHound.py
git checkout bloodhound-ce
pipx install . --force
```

```bash
bloodhound-ce-python
```

### [BloodyAD](https://github.com/CravateRouge/bloodyAD)

```bash
pipx install bloodyAD
```

```bash
bloodyAD --host <IP> -d bloody.local -k set password john.doe 'Password123!'
```

### [kali-linux-labs](https://www.kali.org/tools/kali-meta/#kali-linux-labs)

> *These applications are meant to be insecure & vulnerable to help users experiment in a controlled manner. This metapackage depends on all the packages containing vulnerable environments for safe testing.*

- `dvwa`
- `juice-shop`

```bash
sudo apt install -y kali-linux-labs
```

### [Katana](https://github.com/projectdiscovery/katana)

> *A next-generation crawling and spidering framework*

```bash
sudo apt install -y golang

go install github.com/projectdiscovery/katana/cmd/katana@latest

sudo cp $HOME/go/bin/katana /usr/bin/
```

```bash
katana -u <URL>
```

### [maigret](https://github.com/soxoj/maigret)

> *Collect a dossier on a person by username from thousands of sites*

```bash
pipx install git+https://github.com/soxoj/maigret
pipx ensurepath
```

```bash
maigret <USER>
```

### [pimpmykali](https://github.com/Dewalt-arch/pimpmykali)

> *Kali Linux Fixes for Newly Imported VM's*
>
> ❗ **Read [docs](https://github.com/Dewalt-arch/pimpmykali) before running it! It may contains mods that you do not want.**

```bash
cd /opt
sudo rm -rf pimpmykali/
sudo git clone https://github.com/Dewalt-arch/pimpmykali

sudo /opt/pimpmykali/pimpmykali.sh

# For a new kali vm, run menu option N
```

### [pwntools](https://github.com/Gallopsled/pwntools)

> *Pwntools is a CTF framework and exploit development library. Written in Python, it is designed for rapid prototyping and development, and intended to make exploit writing as simple as possible.*

```bash
sudo apt update && sudo apt install -y python3 python3-pip python3-dev git libssl-dev libffi-dev build-essential
python3 -m pipx install pwntools
```

### [Searchsploit](https://www.exploit-db.com/searchsploit)

```bash
# ~3GB of space necessary
sudo apt update && sudo apt -y install exploitdb

# Update via apt
searchsploit -u
```

### [SecLists](https://github.com/danielmiessler/SecLists)

```bash
seclists() {
    if [[ -d /usr/share/seclists ]];
     then
      echo -e "\n /usr/share/seclists  already exists -- skipping"
     else
      echo -e "\n Download Seclists to /tmp/SecLists.zip"
      sudo wget https://github.com/danielmiessler/SecLists/archive/master.zip -O /tmp/SecList.zip
      echo -e "\n Extracing /tmp/Seclists.zip to /usr/share/seclists"
      sudo unzip -o /tmp/SecList.zip -d /usr/share/seclists
      # rm -f /tmp/SecList.zip
      echo -e "\n Seclists complete" 
    fi
    }
seclists
```

### [Sherlock](https://sherlockproject.xyz/)

> *Hunt down social media accounts by username across social networks*

```bash
# On Kali
sudo apt install sherlock
```

```bash
sherlock <USER>
```

