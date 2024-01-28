# Ubuntu Server - VM

![](.gitbook/assets/ubuntu.svg) [Ubuntu Server](https://ubuntu.com/server/docs)

## First Install - Virtual Machine

- Create a new Virtual Machine in VMWare with preferred specs:
  - **Processors**: `1 CPU - 2 Cores`
    - enable `Virtualize Intel VT-x/EPT or AMD-V/RVI`
  - **RAM** Memory: `2 GB`
  - **Hard Disk**: `15 GB`
  - **CD**: use the **Ubuntu Server LTS** .iso image file - [Download here](https://ubuntu.com/download/server) - and check *Connect at power on*
  - **Network**: set it as `NAT`
  - **Display**: Deactivate `Accelerate 3D graphics`
  - Edit virtual machine and ***Remove***: `Printer`
- Boot the virtual machine and proceed with the [Ubuntu Server Installation](https://ubuntu.com/server/docs/installation)

![](.gitbook/assets/2024-01-28_00-44-11_395.png)

![](.gitbook/assets/2024-01-28_01-16-28_398.png)

**Installation Process**

1. Boot to `Try or Install Ubuntu Server`
2. Choose the language
3. `Update to the new installer` (if offered)
4. Select keyboard layout
5. Select `Ubuntu Server` as base for the install
6. Wait for Network connection to establish via DHCP (continue without networking if this fails)
   - Do not configure a proxy
7. Leave Ubuntu archive mirror as it is (or change based on necessity)
8. For storage, leave `Use an entire disk` checked, and choose a disk to install to
   - `Edit` the `ubuntu-lv` logical volume by assigning it the max size
9. Select `Done` on the configuration screen and confirm the install
10. Enter a username, hostname and password
11. `Skip for now` the Ubuntu Pro upgrade
12. On the SSH Setup, check the `Install OpenSSH server`, select `Done` for the next 2-3 slides
13. Select `Reboot`

---

## First Boot & Update

- Disable **CD** *Connect at power on*
- Boot Ubuntu Server
- Connect through SSH and/or Run the following commands

```bash
sudo apt update -y && sudo apt -y upgrade
```

- Reboot the system

```bash
reboot
```

---

## Configurations

- `SSH` into the VM

```bash
# TIMEZONE
sudo timedatectl set-timezone Europe/Rome

# DISABLE AUTOMATIC UPDATES
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
# make sure all the directives are set to “0”
sudo systemctl disable apt-daily-upgrade.timer
sudo systemctl mask apt-daily-upgrade.service
sudo systemctl disable apt-daily.timer
sudo systemctl mask apt-daily.service

# Change "root" user password
sudo passwd root
```

> Follow the guide here to setup `ZSH` with `Oh-My-Zsh` - [Zsh & Oh-My-Zsh - syselement](https://blog.syselement.com/home/operating-systems/linux/tools/zsh)

---

## Tuning

### Basic Tools

```bash
# Tools
sudo apt install -y apt-transport-https btop ca-certificates curl duf exa gnupg iftop locate nano ncdu neofetch net-tools nload npm pipx software-properties-common sysstat ugrep wget zsh

sudo apt-add-repository ppa:zanchey/asciinema
sudo apt update && sudo apt install asciinema
```

### Docker

```bash
sudo su

# Docker Engine
sh <(curl -sSL https://get.docker.com)

# Docker Compose
LATEST=$(curl -sL https://api.github.com/repos/docker/compose/releases/latest | grep '"tag_name":' | cut -d'"' -f4)
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -sSL https://github.com/docker/compose/releases/download/$LATEST/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
docker compose version

# Add a user to the "docker" group to let it run Docker
sudo groupadd docker
sudo gpasswd -a "${USER}" docker
```

---

## Hardening

...

---

