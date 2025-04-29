# Kali Linux - VM

![](.gitbook/assets/kali.svg) [Kali Linux](https://www.kali.org/)

---

## 🌐 Resources 🔗

> 🔗 [Kali docs](https://www.kali.org/docs/)

---

## [First Install - Virtual Machine](https://www.kali.org/docs/installation/hard-disk-install/)

- Create a new Virtual Machine in VMWare with preferred specs:
  - Processors: `1 CPU - 2 Cores`
  - RAM Memory: `4 GB`
  - Hard Disk: `60 GB`
  - CD: use the Kali Installer .iso image file - [Download here](https://www.kali.org/get-kali/) - and check *Connect at power on*
  - Network: set it as *NAT*
- Boot the virtual machine and proceed with the [Kali Installation](https://www.kali.org/docs/installation/hard-disk-install/)

![](.gitbook/assets/image-20230307160636827.png)

---

## First Boot & Update

- Boot Kali Linux
- Open the Terminal and run the command

```bash
sudo apt update && sudo apt full-upgrade -y
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

- Customize Kali and finish off the setup using `kali-tweaks`

```bash
kali-tweaks
```

![kali-tweaks](.gitbook/assets/image-20230307161431755.png)

### zshell alias - Update packages

```bash
nano ~/.zsh_aliases
```

- Append the following text with the necessary commands to update all packages:

```bash
# Upgrade all packages and remove unused packages

alias updateos='sudo -- sh -c "sudo apt update && sudo apt -y upgrade && sudo apt -y autoremove"'
```

- Download and add hacking platforms `.ovpn` files and set up OpenVpn aliases

```bash
cd
mkdir htb tcm pwnx
# Copy every .ovpn file in the respective dir
```

```bash
# OpenVpn Aliases
alias htbvpn='sudo openvpn --config ~/htb/htb.ovpn --daemon'        # HTB FREE VPN
alias htbvipvpn='sudo openvpn --config ~/htb/htbvip.ovpn --daemon'  # HTB VIP VPN
alias thmvpn='sudo openvpn --config ~/thm/thm.ovpn --daemon'
alias pwnxvpn='sudo openvpn --config ~/pwnx/pwnx.ovpn --daemon'
alias killopenvpn='sudo pkill openvpn'
```

- Save and exit

- Reload the zshell configuration:

```bash
source ~/.zshrc
```

- Test the command

```bash
updateos
```

---

## Configurations

### Basic config

```bash
# Timezone
sudo unlink /etc/localtime
sudo ln -s /usr/share/zoneinfo/Europe/Rome /etc/localtime
sudo timedatectl set-timezone "Europe/Rome"

# GRUB Timeout
sudo sed -E '/^GRUB_TIMEOUT=/s/=(.*)/=1/' -i /etc/default/grub
sudo update-grub

# Chrony NTP
sudo apt install -y chrony
sudo systemctl enable --now chrony
sudo systemctl status chrony --no-pager
sudo timedatectl status
```

### SSH keys

```bash
# Host ssh keys
sudo /bin/rm -v /etc/ssh/ssh_host_*
sudo dpkg-reconfigure openssh-server
sudo systemctl restart ssh

# User ssh key pair
cd
mkdir -p ~/.ssh
cd ~/.ssh
ssh-keygen -t ed25519
# Type a secure passphrase when asked
chmod 700 ~/.ssh
chmod 600 ~/.ssh/*

# Add the SSH private key to the ssh-agent
eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_ed25519
```

### Install [JetBrainsMono Nerd Font](https://www.nerdfonts.com/font-downloads)

```bash
cd
mkdir -p $HOME/.local/share/fonts
cd $HOME/.local/share/fonts
curl -fLO https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip
unzip JetBrainsMono.zip
rm JetBrainsMono.zip

fc-cache -fv
```

### Zsh & Oh-My-Zsh

> Follow the guide here to setup `ZSH` with `Oh-My-Zsh` - [Zsh & Oh-My-Zsh - syselement](../tools/zsh.md)

---

## Personal Kali Config

```bash
# Disable Password prompt for sudo group
sudo sed -i.bak 's/%sudo\s\+ALL=(ALL:ALL) ALL/%sudo ALL=(ALL:ALL) NOPASSWD: ALL/' /etc/sudoers

# KALI Linux XFCE Theme mod #

echo -ne "\n--------Kali Theme Mod--------\n"

## Login: Kali-Dark + Wallpaper + User ##
sudo apt install kali-wallpapers-2020.4
sudo ln -sf /usr/share/backgrounds/kali/kali-neon-16x9.png /usr/share/desktop-base/kali-theme/login/background
sudo sed s:"Kali-Light":"Kali-Dark":g -i /etc/lightdm/lightdm-gtk-greeter.conf
sudo sed '/#greeter-hide-users=false/s/^#//g' -i /etc/lightdm/lightdm.conf

## Set autologin for <username> ##
# sudo nano /etc/lightdm/lightdm.conf
# # In the [Seat:*] section of the file, set the following values:
# autologin-user=<username>
# autologin-user-timeout=0

# Set Adwaita-dark Theme
xfconf-query -c xsettings -p /Net/ThemeName -s "Adwaita-dark"

## Desktop Wallpaper ##
xfconf-query -c xfce4-desktop -p /backdrop/screen0/monitorVirtual1/workspace0/last-image -s /usr/share/backgrounds/kali/kali-neon-16x9.png

## Show Panel on primary display ##
xfconf-query -c 'xfce4-panel' -p '/panels/panel-1/output-name' -t string -s "Primary" --create
## Turn Numlock ON
xfconf-query -c keyboards -p /Default/Numlock -t bool -s true --create
```

```bash
## Set Keybindings - Print, Alt+Shift+D, Win+E, Alt+T ##

nano ~/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-keyboard-shortcuts.xml

# Insert those lines under the first <property name="custom" type="empty">
      <property name="&lt;Alt&gt;t" type="string" value="terminator"/>
      <property name="&lt;Super&gt;e" type="string" value="/usr/bin/Thunar"/>
      <property name="&lt;Shift&gt;&lt;Alt&gt;s" type="string" value="/usr/bin/flameshot gui --clipboard"/>
      <property name="Print" type="string" value="/usr/bin/flameshot gui --clipboard"/>
```

---

## Tools

### Basic Tools

- Install basic tools

```bash
sudo apt install -y apt-transport-https btop curl duf eza fastfetch flameshot gdu htop kali-wallpapers-all locate nano net-tools pipx software-properties-common speedtest-cli sshpass terminator tmux tor tree ugrep vlc wget
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

- Run it with **`subl`** command.

### [Terminator](https://gnome-terminator.org/)

```bash
sudo apt install -y terminator
```

- From the `Default Applications / Utilities` menu, set `terminator` as the default terminal to use

![](.gitbook/assets/image-20230307153127021.png)

```bash
sudo update-alternatives --config x-terminal-emulator
```

![](.gitbook/assets/image-20230307152343811.png)

- [Terminator Shortcuts here](../tools/Terminator-Shortcuts.md)
- Go to `Preferences - Global` and set **Window state** to `Maximized`
- Go to `Preferences - Profiles - Background` and set the **Background** transparency as you like.
- Go to `Preferences - Profiles - Scrolling` and set the checkmark on **Infinite Scrollback**.

```bash
touch ~/.config/terminator/config
```

```bash
# Basic layout with custom font

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

- `CTRL+ALT+T` is a keyboard shortcut already configured in Kali. It should open the `terminator` with your configured layout.

### [Brave](https://brave.com/linux/)

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

### [Github Desktop](https://github.com/shiftkey/desktop)

```bash
wget -qO - https://apt.packages.shiftkey.dev/gpg.key | gpg --dearmor | sudo tee /usr/share/keyrings/shiftkey-packages.gpg > /dev/null

sudo sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/shiftkey-packages.gpg] https://apt.packages.shiftkey.dev/ubuntu/ any main" > /etc/apt/sources.list.d/shiftkey-packages.list'

sudo apt update && sudo apt install -y github-desktop
```

### [VS Codium](https://github.com/VSCodium/vscodium)

```bash
wget https://gitlab.com/paulcarroty/vscodium-deb-rpm-repo/raw/master/pub.gpg && sudo mv pub.gpg /usr/share/keyrings/vscodium-archive-keyring.asc

sudo sh -c 'echo "deb [ signed-by=/usr/share/keyrings/vscodium-archive-keyring.asc ] https://paulcarroty.gitlab.io/vscodium-deb-rpm-repo/debs vscodium main" > /etc/apt/sources.list.d/vscodium.list'

sudo apt update && sudo apt install -y codium
```

### [Docker](https://www.kali.org/docs/containers/installing-docker-on-kali/)

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

---

## Security Tools

### [AutoRecon](https://github.com/Tib3rius/AutoRecon)

> *AutoRecon is a multi-threaded network reconnaissance tool which performs automated enumeration of services. It is intended as a time-saving tool for use in CTFs and other penetration testing environments (e.g. OSCP). It may also be useful in real-world engagements.*

```bash
sudo apt update -y && sudo apt install -y autorecon

mkdir -p ~/scans/autorecon
```

```bash
cd ~/scans/autorecon
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

### [Katana](https://github.com/projectdiscovery/katana)

> *A next-generation crawling and spidering framework*

```bash
sudo apt install -y golang

go install github.com/projectdiscovery/katana/cmd/katana@latest

sudo cp ~/go/bin/katana /usr/bin/
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
python3 -m pip install --upgrade pip
python3 -m pip install --upgrade pwntools
```

### [Searchsploit](https://www.exploit-db.com/searchsploit)

```bash
sudo apt update && sudo apt -y install exploitdb

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

