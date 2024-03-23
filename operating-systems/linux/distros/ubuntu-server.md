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
sudo apt update -y && sudo apt -y dist-upgrade
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
sed -i 's/1";/0";/' /etc/apt/apt.conf.d/20auto-upgrades
sudo systemctl disable apt-daily{,-upgrade}.timer
sudo systemctl mask apt-daily{,-upgrade}.service

# Disable Ubuntu Pro ESM Hook and MOTD Spam - thanks to UnspamifyUbuntu
sudo mv /etc/apt/apt.conf.d/20apt-esm-hook.conf /etc/apt/apt.conf.d/20apt-esm-hook.conf.disabled
sudo sed -i'' -e 's/^\(\s\+\)\([^#]\)/\1# \2/' /etc/apt/apt.conf.d/20apt-esm-hook.conf
sudo sed -Ezi.orig \
  -e 's/(def _output_esm_service_status.outstream, have_esm_service, service_type.:\n)/\1    return\n/' \
  -e 's/(def _output_esm_package_alert.*?\n.*?\n.:\n)/\1    return\n/' \
  /usr/lib/update-notifier/apt_check.py
sudo /usr/lib/update-notifier/update-motd-updates-available --force
sudo sed -i 's/^ENABLED=.*/ENABLED=0/' /etc/default/motd-news

# Change "root" user password
sudo passwd root
```

> - Follow the guide here to setup `ZSH` with `Oh-My-Zsh` - [Zsh & Oh-My-Zsh - syselement](https://blog.syselement.com/home/operating-systems/linux/tools/zsh)
> - Remove unwanted spam with [UnspamifyUbuntu - Github Skyedra](https://github.com/Skyedra/UnspamifyUbuntu)

### Extend Partition and Filesystem

- Add space to disk on the hypervisor

```bash
sudo apt -y install cloud-guest-utils gdisk
df -h

growpart /dev/sda 3
lsblk
sudo resize2fs /dev/sda3

# or this for LVM
sudo lvextend -r -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv

df -h
```

---

## Network

### Static IP

- Set a static IP in the **netplan** `.yaml` if not configured during OS installation.

```bash
# Show listening sockets and running services
sudo ss -atpu

# List available network interfaces and use the interface ens32
ip -br -c a

# Disable cloud-init networking configuration - if necessary
sudo nano /etc/cloud/cloud.cfg.d/subiquity-disable-cloudinit-networking.cfg
# Make sure it is "disabled"
# network: {config: disabled}

# Open the netplan configuration file for editing
sudo nano /etc/netplan/00-installer-config.yaml

    # This is the network config written by 'subiquity'
    network:
      version: 2
      ethernets:
        ens32:
          addresses: [<IP>/24]
          gateway4: <GATEWAY_IP>
          nameservers:
            addresses: [1.1.1.1, 9.9.9.9]
            
# Exit and save

# Apply the netplan configuration changes
sudo netplan apply

# Reboot the system
```



---

## Tuning

### Basic Tools

```bash
# Tools
sudo apt install -y apt-transport-https aptitude btop ca-certificates curl duf gnupg iftop gdu locate nano ncdu neofetch net-tools nload npm pipx software-properties-common speedtest-cli sysstat tree ugrep wget zsh

sudo apt-add-repository ppa:zanchey/asciinema
sudo apt update && sudo apt install asciinema
```

---

### [Docker - Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

```bash
sudo su
apt install curl

# Docker Engine - Convenience Script
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

- Alternative to install Docker Engine (via APT)

```bash
# Install Docker Engine via APT repository

for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt update -y && sudo apt install -y ca-certificates curl gnupg

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg
sudo chmod a+r /usr/share/keyrings/docker.gpg

sudo sh -c 'echo "deb [arch="$(dpkg --print-architecture)" signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | sudo tee /etc/apt/sources.list.d/docker.list'

sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl enable docker --now
sudo gpasswd -a "${USER}" docker

# On Debian and Ubuntu, the Docker service starts on boot by default, if not run
sudo systemctl enable docker.service
sudo systemctl enable containerd.service

# Reboot and Test
reboot
docker run hello-world
```

---

## Hardening

### SSH-key-based authentication

Ubuntu Server with OpenSSH pre-installed comes with `PasswordAuthentication yes` parameter already set inside `/etc/ssh/sshd_config.d/50-cloud-init.conf` (or `/etc/ssh/sshd_config`). If the parameter is commented, the default is `yes` (password auth permitted) for the [sshd_config](https://man7.org/linux/man-pages/man5/sshd_config.5.html).

- Generate an SSH Key Pair on the **local HOST** from which the connection is established

```bash
ssh <sudo_user>@<remote_host_IP>
```

```bash
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

- Add the Public Key to a system/sudo user on the Ubuntu VM

```bash
# Automatic
ssh-copy-id <sudo_user>@<remote_host_IP>
```

```bash
# Manually

# Local HOST
cat ~/.ssh/id_ed25519.pub
# copy the string
# Should start with ssh-ed25519 AAAA... or ssh-rsa AAAA... (if rsa)

# Ubuntu VM
echo pubkey_string >> ~/.ssh/authorized_keys
# Set permissions
chmod -R go= ~/.ssh
```

- Log out and log in using the Private Key

```bash
ssh <sudo_user>@<remote_host_IP>

# ssh -i ~/.ssh/id_ed25519 <sudo_user>@<remote_host_IP>

# Enter the key Passphrase
```

- Disable SSH password authentication

```bash
# Delete sshd_config.d/50-cloud-init.conf
sudo rm  /etc/ssh/sshd_config.d/50-cloud-init.conf

# Inside /etc/ssh/sshd_config set PasswordAuthentication to "no"
sudo sed -i "/^[^#]*PasswordAuthentication[[:space:]]yes/c\PasswordAuthentication no" /etc/ssh/sshd_config

# Restart SSH service
 sudo systemctl restart ssh
```

- Try again to logout and login. Only SSH-key-base authentication is permitted.



... more to come ...

---

