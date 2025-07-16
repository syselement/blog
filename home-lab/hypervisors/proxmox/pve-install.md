# Proxmox VE

![proxmox.com](.gitbook/assets/proxmox-full-lockup-inverted.png)

> **Proxmox Virtual Environment** (Proxmox VE) is an open-source server virtualization platform that lets you manage both virtual machines and containers in a unified environment. It utilizes the Type 1 (bare-metal) KVM hypervisor for full hardware virtualization, providing robust and efficient management of diverse workloads while also offering lightweight container-based virtualization through LXC.

---

## 🌐 Resources 🔗

> - 🔗 [Proxmox Virtual Environment (PVE) Administration Guide](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#_install_proxmox_ve_on_debian)
>   - [Proxmox Virtual Environment - Open-Source Server Virtualization Platform](https://proxmox.com/en/products/proxmox-virtual-environment/overview)
> - ➡️ [Proxmox VE Helper-Scripts](https://community-scripts.github.io/ProxmoxVE/scripts)

---

> 📌 Some of the following commands are based on the [Proxmox VE Helper-Scripts](https://community-scripts.github.io/ProxmoxVE/scripts)
>
> ❗ Use the Proxmox **shell** on the main node via the pve web GUI

## Updating PVE - Manually

- Open the Proxmox shell on the main node (or SSH into PVE -> risky)

```bash
pveupgrade

reboot

# apt update && apt -y dist-upgrade
```

## Post Install

- Use this Proxmox VE Helper-script to
  - Correct Proxmox VE Sources
  - Disable `pve-enterprise` repository
  - Enable `pve-no-subscription` repository
  - Enable `ceph package repositories`
  - Add `pvetest` repository
  - Disable subscription nag (Delete browser cache)
  - Disable high availability
  - Update Proxmox VE

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/misc/post-pve-install.sh)"

# It is recommended to answer “yes” (y) to all options presented during the process.
```

## Kernel Clean

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/misc/kernel-clean.sh)"
```

## Processor Microcode

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/misc/microcode.sh)"
```

## Network configuration

```bash
cat /etc/network/interfaces
```

```bash
auto lo
iface lo inet loopback

iface enp2s0 inet manual

auto vmbr0
iface vmbr0 inet static
        address 192.168.5.2/24
        gateway 192.168.5.254
        bridge-ports enp2s0
        bridge-stp off
        bridge-fd 0

iface wlp3s0 inet manual
```

```bash
cat /etc/resolv.conf
```

```bash
search lan.syselement.com
nameserver 9.9.9.9
nameserver 1.1.1.1
```

```bash
 cat /etc/hosts
```

```bash
127.0.0.1 localhost.localdomain localhost
192.168.5.2 pve.lan.syselement.com pve

# The following lines are desirable for IPv6 capable hosts

::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts
```

---

## Software on PVE

```bash
apt install -y btop duf exa nano net-tools tree
```

### bash Config

- Set custom aliases

```bash
nano ~/.bashrc
```

```bash
# Custom aliases

alias ipa='ip -br -c a'
alias l='exa -lah'
alias la='ls -A'
alias ll='l -T'
alias ls='ls -lh --color=auto'
alias ports='ss -lpntu'
alias updatepve='apt update && apt -y dist-upgrade'
```

### Netdata

> [Netdata - http://192.168.5.2:19999/](http://192.168.5.2:19999/)

```bash

```



### Backup Proxmox Config

> - [ ] TO TRY

- [https://github.com/DerDanilo/proxmox-stuff](https://github.com/DerDanilo/proxmox-stuff)

**Backup**

- Download the [script](https://raw.githubusercontent.com/DerDanilo/proxmox-stuff/master/prox_config_backup.sh)

```bash
cd /root/; wget -qO- https://raw.githubusercontent.com/DerDanilo/proxmox-stuff/master/prox_config_backup.sh
```

- Set the permanent backups directory environment variable or edit the script to set the `$DEFAULT_BACK_DIR` variable to your preferred backup directory

```bash
export BACK_DIR="/path/to/backup/directory"
```

- Make the script executable

```bash
 chmod +x ./prox_config_backup.sh
```

- Shut down ALL VMs + LXC Containers if you want to go the safe way. (Not required)
- Run the script

```bash
./prox_config_backup.sh
```

**Notification**

The script supports [healthchecks.io](https://healthchecks.io/) notifications, either to the hosted service, or a self-hosted instance. The notification sends during the final cleanup stage, and either returns 0 to tell Healthchecks that the command was successful, or the exit error code (1-255) to tell Healthchecks that the command failed. To enable:

- Set the `$HEALTHCHECK` variable to 1
- Set the `$HEALTHCHECK_URL` variable to the full ping URL for your check. Do not include anything after the UUID, the status flag will be added by the script.

------

## [Proxmox Backup Server LXC](https://www.proxmox.com/en/proxmox-backup-server/overview)

> [Proxmox Backup Server - https://192.168.5.3:8007](https://192.168.5.3:8007)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/proxmox-backup-server.sh)"

# PBS Interface <IP>:8007

# Set a root password if using autologin. This will be the PBS password.
# Login to WebGUI and open PBS shell
sudo passwd root
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.

### PBS post install

- Disable the Enterprise Repo
- Add/Correct PBS Sources
- Enable the No-Subscription Repo
- Add Test Repo
- Disable Subscription Nag
- Update and reboot Proxmox Backup Server



Run the command below in the **Proxmox Backup Server Shell** and answer "yes" to all options presented

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/misc/post-pbs-install.sh)"
```



---

## LXC

### LXCs - [Undo Autologin](https://github.com/tteck/Proxmox/discussions/324)

> *If you don't set a root password first, you will not be able to login to the container again, ever.*
>
> 1. set the root password `sudo passwd root`
> 2. remove `--autologin root` from `/etc/systemd/system/container-getty@1.service.d/override.conf`
> 3. reboot

### LXCs - Cleaner

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/misc/clean-lxcs.sh)"
```

### LXCs - Updater

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/misc/update-lxcs.sh)"
```

### LXC - Filesystem Trim

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/misc/fstrim.sh)"
```



### Ubuntu LXC + UniFi Network Server

> [Unifi Network Server - https://192.168.5.10:8443/](https://192.168.5.10:8443/) on Ubuntu LXC

First, install the Ubuntu LXC with the following specs (defaults are 1 vCPU, 512MB, 2 GB) necessary to the UniFi Network Server - use `Advanced Settings` during Helper Script launch:

- 2 vCPU
- 2GB RAM
- 8 GB Disk

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/ct/ubuntu.sh)"

# To Manually Update Ubuntu, run the command above (or type update) in the LXC Console.
```

```bash
# Manual Advanced Settings 
🧩  Using Advanced Settings on node pve
🖥️  Operating System: ubuntu
🌟  Version: 24.04
📦  Container Type: Unprivileged
🔐  Root Password: ********
🆔  Container ID: 110
🏠  Hostname: unifi
💾  Disk Size: 8 GB
🧠  CPU Cores: 2
🛠️  RAM Size: 2048 MiB
🌉  Bridge: vmbr0
📡  IPv4 Address: 192.168.5.10/24
🌐  Gateway IP Address: 192.168.5.254
📡  IPv6: Disabled
📡  APT-Cacher IP Address: Default
⚙️  Interface MTU Size: Default
🔍  DNS Search Domain: Host
📡  DNS Server IP Address: Host
🏷️  Vlan: Default
📡  Tags: community-script;os;unifi
🔑  Root SSH Access: yes
🗂️  Enable FUSE Support: no
🔍  Verbose Mode: yes
```

**PROXMOX** - Network > edit `eth0` and set the Static IP - if not already done by the Advanced installer.

Open the LXC console or SSH into it and proceed with installing the **UniFi Network Controller** manually via the [UniFi Installation/Update Scripts - Ubiquiti Community](https://community.ui.com/questions/UniFi-Installation-Scripts-or-UniFi-Easy-Update-Script-or-UniFi-Lets-Encrypt-or-UniFi-Easy-Encrypt-/ccbc7530-dd61-40a7-82ec-22b17f027776)

- For more commands, check my guide here -> [UniFi Network Server](https://blog.syselement.com/home/operating-systems/linux/tools/unifi)

```bash
# Quick install commands
sudo su

rm unifi-latest.sh &> /dev/null; wget https://get.glennr.nl/unifi/install/install_latest/unifi-latest.sh && bash unifi-latest.sh
```

- Browse the web page - [https://192.168.5.10:8443/](https://192.168.5.10:8443/) and configure the **UniFi Network Server**
  - Adopt your devices



### [Uptime Kuma](https://github.com/louislam/uptime-kuma)

> *DELETED*

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/uptimekuma.sh)"
```



### [AdGuard Home](https://adguard.com/en/adguard-home/overview.html)

> [AdGuard - http://192.168.5.5:3080](http://192.168.5.5:3080)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/adguard.sh)"

# Setup interface <IP>:3000

# To Manually Update AdGuard Home, run the command above (or type update) in the AdGuard LXC Console.
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.



### [Vaultwarden](https://www.vaultwarden.net/)

> [Vaultwarden - http://192.168.5.7:8000](http://192.168.5.7:8000)
>
> [Vaultwarden Admin - http://192.168.5.7:8000/admin](http://192.168.5.7:8000/admin)
>
> [https://vaultwarden.lab.syselement.com/#/login](https://vaultwarden.lab.syselement.com/#/login)
>
> [https://vaultwarden.lab.syselement.com/admin](https://vaultwarden.lab.syselement.com/admin)

- Based on Alpine Linux

```bash
bash -c "$(wget -qO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/alpine-vaultwarden.sh)"

# To Update Alpine-Vaultwarden, or Set the Admin Token, run the command above in the Vaultwarden LXC Console.
# or run
apk update && apk upgrade
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.

Set `https://vaultwarden.lab.syselement.com` in the **General settings - Domain URL** admin menu [http://192.168.5.7:8000/admin](http://192.168.5.7:8000/admin).

- Vaultwarden needs to be behind a proxy (e.g. [Zoraxy](#zoraxy-lxc)) to obtain HTTPS and to allow clients to connect.



### [Zoraxy](https://zoraxy.arozos.com/)

> [Zoraxy - http://192.168.5.6:8000/](http://192.168.5.6:8000/)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/zoraxy.sh)"

# To Manually Update Zoraxy, run the command above (or type update) in the Zoaxy LXC Console.
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.

- `Set Proxy Root` to `localhost:8080`
- `Status` - set `Use TLS to serve proxy request` and `Start Service`
- `Create Proxy Rules` - new proxy rule for **Vaultwarden**
  - **Proxy Type** - `Sub-domain`
  - **Subdomain Matching Keyword** - `vaultwarden.lab.syselement.com`
  - **Target IP** - `192.168.5.7:8000` (*Vaultwarden LXC IP*)
  - *Create Endpoint*

**Local HOST/DNS** - set `vaultwarden.lab.syselement.com` to *Zoraxy LXC IP* (or forward port `80` and `443` from your router to your Zoraxy LXC IP).

```bash
# e.g. C:\Windows\System32\drivers\etc\hosts
192.168.5.6 vaultwarden.lab.syselement.com
192.168.5.6 wiki.lab.syselement.com
```

- Check [Technitium DNS](#technitium-dns-lxc) configuration too and use the Technitium server IP as DNS Server.



### [Wiki.js](https://js.wiki/)

> [Wiki.js - http://192.168.5.16:3000/](http://192.168.5.16:3000/)
>
> [https://wiki.lab.syselement.com/login](https://wiki.lab.syselement.com/login)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/wikijs.sh)"

# Wiki.js Interface <IP>:3000

# To Manually Update Wiki.js, run the command above (or type update) in the Wiki.js LXC Console.
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.



### [Technitium DNS](https://technitium.com/dns/)

> [Technitium - http://192.168.5.11:5380/](http://192.168.5.11:5380/)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/technitiumdns.sh)"

# Technitium DNS Interface <IP>:5380

# To Manually Update Technitium DNS, run the command above (or type update) in the Technitium DNS LXC Console.
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.

Open the webpage and navigate to **Zones**

- `Add Zone` - Primary Zone: `lab.syselement.com`

- Enter the `lab.syselement.com` zone
  - `Add Record`
    - **Name**: `vaultwarden`
    - **IPv4 Address**: `192.168.5.6`
    - Save it
  - Add another record for `wiki` with the same IP

**Settings - Blocking**

- `Enable Blocking`
- Allow/Block List URLs - `Quick Add` - e.g. `Steven Black...`
- Save Settings

**Settings - Proxy & Forwarders**

- Forwarders - `Quick Select` - e.g. `Quad9 Secure (DNS-over-HTTPS)`
- Save Settings

> 📌 To use Techitium as a DNS server, set its IP `192.168.5.11` as DNS server in the client PC network configuration
>
> ```bash
> # e.g. Windows
> ipconfig /all
> 
> DNS Servers . . . : 
> 	192.168.5.11
> 	9.9.9.9
> 		DoH: https://dns.quad9.net/dns-query
> ```



### [PiHole](https://pi-hole.net/)

> OFF

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/pihole.sh)"

# Reboot Pi-hole LXC after install
#P i-hole Interface <IP>/admin

# To set your password:
pihole -a -p
```



### [Homepage](https://gethomepage.dev/latest/)

> *DELETED*

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/homepage.sh)"

# Homepage Interface: IP:3000

# To Manually Update Homepage, run the command above (or type update) in the Homepage LXC Console.
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.

- Configuration (bookmarks.yaml, services.yaml, widgets.yaml) path

```bash
cd /opt/homepage/config/
```



### [Runtipi](https://runtipi.io/)

> [Runtipi - http://192.168.5.14/dashboard](http://192.168.5.14/dashboard)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/runtipi.sh)"
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.



### [Prometheus](https://prometheus.io/)

> [Prometheus - http://192.168.5.15:9090/](http://192.168.5.15:9090/)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/prometheus.sh)"
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.



### [Jellyfin Media Server](https://jellyfin.org/)

> [Jellyfin - http://192.168.5.9:8096/](http://192.168.5.9:8096/)

> 🌐 Resources
>
>
> - [Jellyfin - Proxmox VE Helper-Scripts](https://community-scripts.github.io/ProxmoxVE/scripts?id=jellyfin)
> - [awesome-jellyfin/awesome-jellyfin: A Collection of Awesome Jellyfin Plugins, Themes, Guides and More!](https://github.com/awesome-jellyfin/awesome-jellyfin)
> - [Split A GPU Between Multiple Computers - Proxmox LXC (Unprivileged)](https://www.youtube.com/watch?v=0ZDr5h52OOE)

**Proxmox**

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/ct/jellyfin.sh)"
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.

- Comes already with Privileged/Unprivileged **Hardware Acceleration Support**
  - FFmpeg path: `/usr/lib/jellyfin-ffmpeg/ffmpeg`
  - For NVIDIA graphics cards, you'll need to install the same drivers in the container that you did on the host. In the container, run the driver installation script and add the CLI arg `--no-kernel-module`
- Location of config file

```bash
cd /etc/jellyfin/
```

- Configure [Transcoding](https://jellyfin.org/docs/general/post-install/transcoding/) (and Hardware Acceleration) in the Jellyfin WebUI



**Windows**

- Installed on Windows via `exe` at [https://repo.jellyfin.org/?path=/server/windows/latest-stable/amd64](https://repo.jellyfin.org/?path=/server/windows/latest-stable/amd64)
- **Update**
  1. Download the latest version.
  2. Close or Stop Jellyfin (service) if it is running.
  3. Run the installer.
  4. If everything was completed successfully, the new version is installed.
  5. Run `services.msc`
     - open `Jellyfin Server` service properties
     - set Log On to `Local System account`
     - save and start the service







### [BookStack](https://www.bookstackapp.com/)

> [BookStack - http://192.168.5.164/](http://192.168.5.164/)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/bookstack.sh)"

# BookStack Interface <IP>:80

# Bookstack works only with static ip. If you Change the IP of your LXC, you Need to edit the .env File nano /opt/bookstack/.env

# To Manually Update BookStack, run the command above (or type update) in the BookStack LXC Console.
```

```bash
# Default Login Credentials
# Username:
admin@admin.com
# Password:
password
```



### [phpIPAM](https://phpipam.net/)

> [phpIPAM - http://192.168.5.8](http://192.168.5.8)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/phpipam.sh)"

# Run the "3 Advanced Setting" to set static IP
# phpIPAM Interface <IP>:80

# To Manually Update phpIPAM, run the command above (or type update) in the phpIPAM LXC Console.
```

```bash
# Default Login Credentials
# Username:
Admin
# Password:
ipamadmin
```



### [Cosmos Cloud](https://cosmos-cloud.io/)

> [Cosmos Cloud - http://192.168.5.159/](http://192.168.5.159/)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/cosmos.sh)"

# Cosmos Interface <IP>:80

# To Manually Update Cosmos, run the command above (or type update) in the Cosmos LXC Console.
```



### [Kavita](https://www.kavitareader.com/)

> [Kavita - http://192.168.5.145:5000/](http://192.168.5.145:5000/)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/kavita.sh)"

# Kavita Interface <IP>:5000

# To enable folder adding append your lxc.conf on your host with 'lxc.environment: DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=1'

# To Manually Update Kavita, run the command above (or type update) in the Kavita LXC Console.
```



### [NetBox](https://netboxlabs.com/oss/netbox/)

> - [ ] TO TRY

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/netbox.sh)"
```



### Checkmk

> - [ ] TO TRY
>
> - https://www.youtube.com/watch?v=Rb1_7gbgS7k

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/checkmk.sh)"
```



### Kasm

> - [ ] TO TRY

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/checkmk.sh)"
```



------

## Ubuntu Server VM

> 🔗 ➡️ My [Ubuntu Server - VM](../../../operating-systems/linux/distros/ubuntu-server.md#first-boot-and-update) additional/updated guide

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/vm/ubuntu2404-vm.sh)"
```

Turn OFF the VM (if ON).

Follow the instruction at [Useful Ubuntu 22.04 VM Commands](https://github.com/community-scripts/ProxmoxVE/discussions/272) to set up Cloud-Init on the VM:

- User
- Password
- SSH public key for SSH Key login
- Upgrade packages - `No`
- Static IP (may need DHCP)
- Click `Regenerate Image`

Start the VM.

- Open the VM Console using `xterm.js`



### Resize disk

**PROXMOX** - Hardware > Hard Disk (scsi0) > Disk Action > `Resize`

In the VM Console:

```bash
sudo parted /dev/sda
resizepart 1
# Fix
# Partition number: 1
# Yes
# End? -0
quit

sudo reboot
```



### First Config

- **SSH**

```bash
### Settings for SSH with Password
sudo sed -i -e 's/^PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config && sudo systemctl restart sshd

#### Settings SSH with SSH Key + Disable root login
# Paste your SSH Public Key into ~/.ssh/authorized_keys (if not set by Proxmox Cloud-Init) and set sshd_config accordingly
sudo nano /etc/ssh/sshd_config
# Paste these lines
PermitRootLogin no
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
AuthenticationMethods publickey
PubkeyAuthentication yes
PermitEmptyPasswords no


# Save and exit the file
# Restart the sshd service
sudo systemctl restart sshd

# Check sshd config with
sudo sshd -T
```

- Timezone and Updates

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



### Software

- `SSH` into the VM

```bash
sudo apt update -y && sudo apt -y upgrade

sudo apt install -y btop curl duf eza iftop locate nano ncdu fastfetch net-tools nload npm pipx qemu-guest-agent sysstat ugrep wget zsh
```

```bash
sudo apt-add-repository ppa:zanchey/asciinema
sudo apt update && sudo apt install asciinema
```



### Zsh & Oh-My-Zsh

> Follow the guide here to setup `ZSH` with `Oh-My-Zsh` - [Zsh & Oh-My-Zsh - syselement](../../../operating-systems/linux/tools/zsh.md)



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

# Add the current user to the "docker" group to let it run Docker
sudo groupadd docker
sudo gpasswd -a "${USER}" docker
```

#### [Dockge](https://github.com/louislam/dockge)

> [Dockge - http://192.168.5.200:5001](http://192.168.5.200:5001/)

```bash
mkdir -p /opt/{dockge,stacks}
wget -q -O /opt/dockge/compose.yaml https://raw.githubusercontent.com/louislam/dockge/master/compose.yaml
cd /opt/dockge
docker compose up -d
```

#### [Portainer](https://docs.portainer.io/)

> [Portainer - https://192.168.5.200:9443](https://192.168.5.200:9443/)

```bash
# Install Portainer
docker volume create portainer_data
docker run -d \
  -p 8000:8000 \
  -p 9443:9443 \
  --name=portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

- [Updating Docker Standalone Portainer](https://docs.portainer.io/start/upgrade/docker)
  - Go to Settings > Back up Portainer - Download backup file
  - Proceed with updating

```bash
# Update Portainer
docker stop portainer
docker rm portainer
docker pull portainer/portainer-ce:2.20.2
docker run -d -p 8000:8000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:2.20.2
```

#### [MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF)

```bash
sudo mkdir -p ~/docker/mobsf

sudo chown 9901:9901 ~/docker/mobsf

docker run -it --rm --name mobsf -p 8010:8010 -v ~/docker/mobsf:/home/mobsf/.MobSF opensecurity/mobile-security-framework-mobsf:latest
```

#### [WatchYourLan](https://github.com/aceberg/WatchYourLAN)

> [WatchYourLan - http://192.168.5.200:8840/](http://192.168.5.200:8840/)

```bash
docker run -d --name wyl \
        -e "IFACE=eth0" \
        -e "TZ=Europe/Rome" \
        --network="host" \
        -v watchyourlan_data:/data \
    aceberg/watchyourlan
```



### [Tailscale](https://tailscale.com/)

- Login to [Tailscale](https://login.tailscale.com/)
- Open the VM shell and run:

```bash
curl -fsSL https://tailscale.com/install.sh | sh

sudo tailscale up

# Follow the instruction to register the device
```

[Tailscale SSH](https://tailscale.com/kb/1193/tailscale-ssh)

- On the host being connected to, you need to advertise that Tailscale is managing SSH connections which originate from the Tailscale network to this host

```bash
sudo tailscale up --ssh

# This generates a host keypair, shares its public half with the Tailscale control plane for distribution to clients, and configures tailscaled to intercept all traffic from your tailnet that is routed to port 22 on the Tailscale IP address. This SSH initialization only needs to be done once per host.
```



### [microk8s](https://microk8s.io/docs/getting-started)

- Install microk8s

```bash
sudo snap install microk8s --classic

###
sudo usermod -a -G microk8s $USER
sudo mkdir -p ~/.kube
sudo chmod 0700 ~/.kube
sudo chown -f -R $USER ~/.kube

# Close SSH session and reopen it

microk8s status --wait-ready
```

- Some commands

```bash
microk8s stop
microk8s start

microk8s kubectl get nodes
microk8s kubectl get services
microk8s kubectl get pods

microk8s enable dns
microk8s enable hostpath-storage
microk8s enable ingress
microk8s enable core/metrics-server

# Community Add-ons repository
microk8s enable community
microk8s enable portainer
# microk8s disable portainer
```

- Set `.kube/config` file for [k9s](https://k9scli.io/topics/install/)

```bash
microk8s.kubectl config view --raw > $HOME/.kube/config

# Install k9s
 brew install derailed/k9s/k9s
 # Run it and check microk8s cluster
 k9s
```



### BookStack (only on fresh Ubuntu)

> 🔗 [BookStack Admin Documentation - Installation](https://www.bookstackapp.com/docs/admin/installation/)
>
> ​	🔗 [docker-bookstack](https://github.com/linuxserver/docker-bookstack)
>
> ​	🔗 [Ubuntu 24.04 Installation Script](https://www.bookstackapp.com/docs/admin/installation/#ubuntu-2404)

- Install a fresh [Ubuntu Server VM](#ubuntu-server-vm)

- SSH into the Ubuntu VM and run the `BookStack` Ubuntu Installation script

> ❗ A script to install BookStack on a fresh instance of **Ubuntu 24.04** is available. This script is ONLY FOR A FRESH OS, it will install **Apache, MySQL 8.0 & PHP 8.3** and could OVERWRITE any existing web setup on the machine. It also does not set up mail settings or configure system security so you will have to do those separately. You can use the script as a reference if you’re installing on a non-fresh machine.

```bash
# Download the script
wget https://raw.githubusercontent.com/BookStackApp/devops/main/scripts/installation-ubuntu-22.04.sh

# Make it executable
chmod a+x installation-ubuntu-22.04.sh

# Run the script with admin permissions
sudo ./installation-ubuntu-22.04.sh

# Set the VM IP as domain during the first run of BookStack
```

> 📌 Default login: `admin@admin.com`:`password`



---

## [Alpine VM](https://community-scripts.github.io/ProxmoxVE/scripts?id=alpine)

> - [ ] TO TRY

> [10 Alpine Linux apk Command Examples - nixCraft](https://www.cyberciti.biz/faq/10-alpine-linux-apk-command-examples/)

```bash
bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/alpine.sh)"
```

```bash
# To update
apk update && apk upgrade
```

------

