# ParrotOS - VM

![](.gitbook/assets/parrot.svg) [ParrotOS](https://www.parrotsec.org/)

---

## 🌐 Resources 🔗

> 🔗 [Parrot Security Blog - Release Notes](https://parrotsec.org/blog/)

---

## [First Install - Virtual Machine](https://parrotsec.org/docs/virtualization/parrot-on-vmware)

- Create a new Virtual Machine in VMWare with preferred specs:
  - **Processors**: `1 CPU - 2 Cores`
  - **RAM** Memory: `4 GB`
  - **Hard Disk**: `40 GB`
  - **CD**: use the Parrot Installer .iso image file - [Download here](https://parrotsec.org/download/) - and check *Connect at power on*
  - **Network**: set it as `NAT`
  - Edit virtual machine and ***Remove***: `Printer`
- Boot the virtual machine and proceed with the [ParrotOS Installation](https://parrotsec.org/docs/installation/)

![](.gitbook/assets/image-20230611192331531.png)

---

## First Boot & Update

- Disable **CD** *Connect at power on*
- Boot ParrotOS
- Open the Terminal and run the command

```bash
sudo parrot-upgrade
# or
sudo apt update && sudo apt full-upgrade
```

- Reboot the system

```bash
reboot
```

### bash alias - Update packages

```bash
cp /root/.bashrc ~
nano ~/.bashrc
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
alias htbvpn='sudo openvpn --config ~/htb/htb.ovpn --daemon'
alias thmvpn='sudo openvpn --config ~/thm/thm.ovpn --daemon'
alias pwnxvpn='sudo openvpn --config ~/pwnx/pwnx.ovpn --daemon'
alias killopenvpn='sudo pkill openvpn'
```

- Save and exit

- Reload the bash configuration:

```bash
source ~/.bashrc
```

- Test the command

```bash
updateos
```

------

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

------

## Tools

### Basic Tools

- Install basic tools

```bash
sudo apt install -y apt-transport-https btop curl duf eza fastfetch flameshot gdu htop locate nano net-tools pipx software-properties-common speedtest-cli sshpass terminator tmux tor tree ugrep vlc wget
```

### [Sublime](https://www.sublimetext.com/docs/linux_repositories.html)

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

- Run it with **`subl`** command.

### [Terminator](https://gnome-terminator.org/)

```bash
sudo apt install -y terminator
```

- From the `System / Preferences / Personal / Preferred Applications` menu, set `terminator` as the default terminal to use

![](.gitbook/assets/2023-07-04_19-18-18_155.png)

```bash
sudo update-alternatives --config x-terminal-emulator
```

![](.gitbook/assets/2023-07-04_19-19-36_156.png)

- [Terminator Shortcuts here](../tools/Terminator-Shortcuts.md)
- Go to `Preferences - Global` and set **Window state** to `Maximized`
- Go to `Preferences - Profiles - General` and set the **Font** to `Monotspace Regular 16`.
- Go to `Preferences - Profiles - Background` and set the **Background** transparency as you like.
- Go to `Preferences - Profiles - Scrolling` and set the checkmark on **Infinite Scrollback**.
- The same above preferences can be configure within the following config file

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

- `ALT+T` is a keyboard shortcut already configured in ParrotOS. It should open the `terminator` with your configured layout.

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

- ❗ Already present in ParrotOS, if not, follow the commands below

```bash
sudo wget -q https://gitlab.com/paulcarroty/vscodium-deb-rpm-repo/raw/master/pub.gpg && sudo mv pub.gpg /usr/share/keyrings/vscodium-archive-keyring.asc

sudo sh -c 'echo "deb [ signed-by=/usr/share/keyrings/vscodium-archive-keyring.asc ] https://paulcarroty.gitlab.io/vscodium-deb-rpm-repo/debs vscodium main" > /etc/apt/sources.list.d/vscodium.list'

sudo apt update && sudo apt install -y codium
```

### [Docker](https://docs.docker.com/engine/install/debian/)

🔗 [Parrot OS Weird Docker Installation Issues](https://flarexes.com/parrot-os-weird-docker-installation-issues)

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

# Disable the services at boot
sudo systemctl disable docker.service containerd.service
# still has docker.socket active to start the Docker service only when necessary

# Test
docker run hello-world
```

------

