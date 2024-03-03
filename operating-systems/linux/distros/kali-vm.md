# Kali Linux - VM

![](.gitbook/assets/kali.svg) [Kali Linux](https://www.kali.org/)

## First Install - Virtual Machine

- Create a new Virtual Machine in VMWare with preferred specs:
  - Processors: `1 CPU - 2 Cores`
  - RAM Memory: `4 GB`
  - Hard Disk: `40 GB`
  - CD: use the Kali Installer .iso image file - [Download here](https://www.kali.org/get-kali/) - and check *Connect at power on*
  - Network: set it as *NAT*
- Boot the virtual machine and proceed with the [Kali Installation](https://www.kali.org/docs/installation/hard-disk-install/)

![](.gitbook/assets/image-20230307160636827.png)

![Kali Install](.gitbook/assets/kali-install.gif)

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

![Kali Upgrade](.gitbook/assets/kali-upgrade.gif)

- Customize Kali and finish off the setup using `kali-tweaks`

```bash
kali-tweaks
```

![kali-tweaks](.gitbook/assets/image-20230307161431755.png)

### zshell alias - Update packages

```bash
nano ~/.zshrc
```

- Append the following text with the necessary commands to update all packages:

```bash
# Upgrade all packages and remove unused packages

alias updateos='sudo -- sh -c "sudo apt update && sudo apt -y upgrade && sudo apt -y autoremove"'
```

- Download and add hacking platforms `.ovpn` files and set up OpenVpn aliases

```bash
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

- Save and exit.

- Reload the zshell configuration:

```bash
source ~/.zshrc
```

- Test the command

```bash
updateos
```

![](.gitbook/assets/image-20230307150035865.png)

---

## Configurations

### GRUB Timeout

- Set the seconds in the GRUB_TIMEOUT value to `1`

```bash
sudo sed -E '/^GRUB_TIMEOUT=/s/=(.*)/=1/' -i /etc/default/grub
sudo update-grub
reboot

# Or edit it manually
sudo nano /etc/default/grub
```

### NTP

```bash
sudo apt install -y chrony
sudo systemctl enable --now chrony
sudo systemctl status chrony --no-pager
sudo timedatectl status
```



### New SSH Keys

```bash
sudo /bin/rm -v /etc/ssh/ssh_host_*
sudo dpkg-reconfigure openssh-server
sudo systemctl restart ssh
ssh-keygen -t rsa
```

### Zsh & Oh-My-Zsh

> Follow the guide here to setup `ZSH` with `Oh-My-Zsh` - [Zsh & Oh-My-Zsh - syselement](https://blog.syselement.com/home/operating-systems/linux/tools/zsh)

---

## Tools

### Basic Tools

- Install basic tools

```bash
sudo apt install -y apt-transport-https btop curl duf eza flameshot htop kali-wallpapers-all neofetch net-tools pipx speedtest-cli telegram-desktop terminator tor tree vlc wget
```

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

- Set up your layout. `CTRL+SHIFT+O` and `CTRL+SHIFT+E` in my case.
  - [Terminator Shortcuts here](../tools/Terminator-Shortcuts.md)

- Go to `Preferences - Global` and set **Window state** to `Maximized`
- Go to `Preferences - Profiles - Background` and set the **Background** transparency as you like.
- Go to `Preferences - Profiles - Scrolling` and set the checkmark on **Infinite Scrollback**.
- Go to `Preferences - Layouts` and click on `Add` button to create a new layout and give it a name.
- This should create the `~/.config/terminator/config` file.
- Now you can start `terminator` using the saved layout using: 

```bash
terminator -l <yourLayout>
```

- Edit the `~/.config/terminator/config` file, rename *yourLayout* to `default` and remove/rename the previous default layout. Now, when Terminator starts without any parameters, it will load your custom [[default]] layout!
  - Edit the [[[terminalx]]]/ `command = neofetch;zsh` for the desired terminal, so that it will run your custom command during startup!!

```bash
nano ~/.config/terminator/config
```

```bash
[global_config]
  window_state = maximise
  suppress_multiple_term_dialog = True
[keybindings]
[profiles]
  [[default]]
    background_darkness = 0.99
    background_type = transparent
    scrollback_infinite = True
    palette = "#1f2229:#d41919:#5ebdab:#fea44c:#367bf0:#9755b3:#49aee6:#e6e6e6:#198388:#ec0101:#47d4b9:#ff8a18:#277fff:#962ac3:#05a1f7:#ffffff"
    use_theme_colors = True
    title_transmit_bg_color = "#1c71d8"
    title_receive_bg_color = "#3584e4"
[layouts]
#  [[default]]
#    [[[window0]]]
#      type = Window
#      parent = ""
#    [[[child1]]]
#      type = Terminal
#      parent = window0
#      profile = default
  [[default]]
    [[[child0]]]
      type = Window
      parent = ""
      order = 0
      position = 0:35
      maximised = True
      fullscreen = False
      size = 2550, 1529
      title = syselement@syskali: ~
      last_active_term = bad1ac94-7015-4914-b44a-dbeb25a78357
      last_active_window = True
    [[[child1]]]
      type = VPaned
      parent = child0
      order = 0
      position = 762
      ratio = 0.5
    [[[terminal2]]]
      type = Terminal
      parent = child1
      order = 0
      profile = default
      uuid = bad1ac94-7015-4914-b44a-dbeb25a78357
      command = neofetch;zsh
    [[[child3]]]
      type = HPaned
      parent = child1
      order = 1
      position = 1272
      ratio = 0.499803536345776
    [[[terminal4]]]
      type = Terminal
      parent = child3
      order = 0
      profile = default
      uuid = f9bdedfc-607c-4975-abaa-7c64fc7076b2
    [[[terminal5]]]
      type = Terminal
      parent = child3
      order = 1
      profile = default
      uuid = e7f2289c-ce5b-4b54-ba1c-59cdad3b77c3
[plugins]
```

- **`CTRL+ALT+T`** is a keyboard shortcut already configured in Kali. It should open the `terminator` with your configured layout.

![](.gitbook/assets/image-20230307154541533.png)

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

### [Brave](https://brave.com/linux/)

```bash
sudo sh -c '
    apt install -y curl &&
    curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg &&
    echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main" | tee /etc/apt/sources.list.d/brave-browser-release.list &&
    apt update &&
    apt install -y brave-browser
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

## Offensive Sec Tools

### [Searchsploit](https://www.exploit-db.com/searchsploit)

```bash
sudo apt update && sudo apt -y install exploitdb

searchsploit -u
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

### [AutoRecon](https://github.com/Tib3rius/AutoRecon)

> *AutoRecon is a multi-threaded network reconnaissance tool which performs automated enumeration of services. It is intended as a time-saving tool for use in CTFs and other penetration testing environments (e.g. OSCP). It may also be useful in real-world engagements.*

```bash
sudo apt update

sudo apt install -y python3 python3-pip seclists curl dnsrecon enum4linux feroxbuster gobuster impacket-scripts nbtscan nikto nmap onesixtyone oscanner redis-tools smbclient smbmap snmp sslscan sipvicious tnscmd10g whatweb wkhtmltopdf

sudo apt install -y python3-venv
python3 -m pip install --user pipx
python3 -m pip install --upgrade pipx
python3 -m pipx ensurepath
source ~/.zshrc
#source ~/.bashrc

pipx install git+https://github.com/Tib3rius/AutoRecon.git

mkdir -p ~/scans/autorecon
```

```bash
cd ~/scans/autorecon
autorecon <TARGET-IP>
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

### [pwntools](https://github.com/Gallopsled/pwntools)

> *Pwntools is a CTF framework and exploit development library. Written in Python, it is designed for rapid prototyping and development, and intended to make exploit writing as simple as possible.*

```bash
sudo apt update && sudo apt install python3 python3-pip python3-dev git libssl-dev libffi-dev build-essential
python3 -m pip install --upgrade pip
python3 -m pip install --upgrade pwntools
```

### [Katana](https://github.com/projectdiscovery/katana)

> *A next-generation crawling and spidering framework*

```bash
sudo apt install -y golang

go install github.com/projectdiscovery/katana/cmd/katana@latest

sudo cp ~/go/bin/katana /bin/
```

```bash
katana -u <URL>
```

