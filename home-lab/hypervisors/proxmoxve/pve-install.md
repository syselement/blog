# Proxmox VE

> - 🔗 [Proxmox Virtual Environment (PVE) Administration Guide](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#_install_proxmox_ve_on_debian)
> - ➡️ [Proxmox VE Helper-Scripts](https://tteck.github.io/Proxmox/)

📌 Some of the following commands are based on the [Proxmox VE Helper-Scripts](https://tteck.github.io/Proxmox/).

## Updating PVE - Manually

- SSH into PVE

```bash
apt update && apt -y dist-upgrade
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
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"

# It is recommended to answer “yes” (y) to all options presented during the process.
```

## Kernel Clean

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/kernel-clean.sh)"
```

## Processor Microcode

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/microcode.sh)"
```

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

------

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

------

## LXC

### LXCs - [Undo Autologin](https://github.com/tteck/Proxmox/discussions/324)

> *If you don't set a root password first, you will not be able to login to the container again, ever.*
>
> 1. set the root password `sudo passwd root`
> 2. remove `--autologin root` from `/etc/systemd/system/container-getty@1.service.d/override.conf`
> 3. reboot

### LXCs - Cleaner

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/clean-lxcs.sh)"
```

### LXCs - Updater

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/update-lxcs.sh)"
```



### [Uptime Kuma LXC](https://github.com/louislam/uptime-kuma)

> *DELETED*

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/ct/uptimekuma.sh)"
```



### [AdGuard Home LXC](https://adguard.com/en/adguard-home/overview.html)

> [http://192.168.5.5:3080](http://192.168.5.5:3080)

**PROXMOX** - Network > edit `eth0` and set the Static IP.

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/ct/adguard.sh)"

# Setup interface <IP>:3000

# To Manually Update AdGuard Home, run the command above (or type update) in the AdGuard LXC Console.
```



### [Vaultwarden LXC](https://www.vaultwarden.net/)

> [http://192.168.5.7:8000](http://192.168.5.7:8000)
>
> [http://192.168.5.7:8000/admin](http://192.168.5.7:8000/admin)
>
> [https://vaultwarden.syselement.com/#/login](https://vaultwarden.syselement.com/#/login)
>
> [https://vaultwarden.syselement.com/admin](https://vaultwarden.syselement.com/admin)

- Based on Alpine Linux

```bash
bash -c "$(wget -qO - https://github.com/tteck/Proxmox/raw/main/ct/alpine-vaultwarden.sh)"

# To Update Alpine-Vaultwarden, or Set the Admin Token, run the command above in the Vaultwarden LXC Console.
# or run
apk update && apk upgrade
```

**PROXMOX** - Network > edit `eth0` and set the Static IP.

Set `https://vaultwarden.syselement.com` in the **General settings - Domain URL** admin menu [http://192.168.5.7:8000/admin](http://192.168.5.7:8000/admin).

- Vaultwarden needs to be behind a proxy (e.g. [Zoraxy](#zoraxy-lxc)) to obtain HTTPS and to allow clients to connect.



### [Zoraxy LXC](https://zoraxy.arozos.com/)

> [http://192.168.5.6:8000/](http://192.168.5.6:8000/)

**PROXMOX** - Network > edit `eth0` and set the Static IP.

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/ct/zoraxy.sh)"
```

- `Set Proxy Root` to `localhost:8080`
- `Status` - set `Use TLS to serve proxy request` and `Start Service`
- `Create Proxy Rules` - new proxy rule for **Vaultwarden**
  - **Proxy Type** - `Sub-domain`
  - **Subdomain Matching Keyword** - `vaultwarden.syselement.com`
  - **Target IP** - `192.168.5.7:8000` (*Vaultwarden LXC IP*)
  - *Create Endpoint*

**Local HOST/DNS** - set `vaultwarden.syselement.com` to *Zoraxy LXC IP* (or forward port `80` and `443` from your router to your Zoraxy LXC IP).

```bash
# e.g.
192.168.5.6 vaultwarden.syselement.com
192.168.5.6 wiki.syselement.com
```



### [Wiki.js LXC](https://js.wiki/)

> [http://192.168.5.10:3000/](http://192.168.5.10:3000/)
>
> [https://wiki.syselement.com/login](https://wiki.syselement.com/login)

**PROXMOX** - Network > edit `eth0` and set the Static IP.

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/ct/wikijs.sh)"

# Wiki.js Interface <IP>:3000

# To Manually Update Wiki.js , run the command above (or type update) in the Wiki.js LXC Console.
```



### [Technitium DNS LXC](https://technitium.com/dns/)

**PROXMOX** - Network > edit `eth0` and set the Static IP.

> [http://192.168.5.11:5380/](http://192.168.5.11:5380/)

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/ct/technitiumdns.sh)"

# Technitium DNS Interface <IP>:5380
```



### [PiHole](https://pi-hole.net/)

> OFF

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/ct/pihole.sh)"

# Reboot Pi-hole LXC after install
#P i-hole Interface <IP>/admin

# To set your password:
pihole -a -p
```



------

## Ubuntu Server VM

> My [Ubuntu Server - VM](https://blog.syselement.com/home/operating-systems/linux/distros/ubuntu-server#first-boot-and-update) additional guide

```bash
bash -c "$(wget -qLO - https://raw.githubusercontent.com/tteck/Proxmox/main/vm/ubuntu-vm.sh)"
```

Turn OFF the VM (if ON).

Follow the instruction at [Useful Ubuntu 22.04 VM Commands](https://github.com/tteck/Proxmox/discussions/2072) to set up Cloud-Init on the VM:

- User
- Password
- SSH public key for SSH Key login
- Upgrade packages - `No`
- Static IP
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
sudo apt install -y btop curl duf eza iftop locate nano ncdu neofetch net-tools nload npm pipx qemu-guest-agent sysstat ugrep wget zsh
```

```bash
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



#### [MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF)

```bash
sudo mkdir -p ~/docker/mobsf

sudo chown 9901:9901 ~/docker/mobsf

docker run -it --rm --name mobsf -p 8010:8010 -v ~/docker/mobsf:/home/mobsf/.MobSF opensecurity/mobile-security-framework-mobsf:latest
```



### Zsh & Oh-My-Zsh

> Follow the guide here to setup `ZSH` with `Oh-My-Zsh` - [Zsh & Oh-My-Zsh - syselement](https://blog.syselement.com/home/operating-systems/linux/tools/zsh)

---

## Alpine VM

> [10 Alpine Linux apk Command Examples - nixCraft](https://www.cyberciti.biz/faq/10-alpine-linux-apk-command-examples/)





---

## BookStack VM

- [ ] to do

> 🔗 [BookStack Admin Documentation - Installation](https://www.bookstackapp.com/docs/admin/installation/)
>
> 🔗 [docker-bookstack](https://github.com/linuxserver/docker-bookstack)
>
> 🔗 [Ubuntu 22 Installation Script](https://www.bookstackapp.com/docs/admin/installation/#ubuntu-2204-installation-script)

- Install an [Ubuntu VM](#ubuntu-vm)

- SSH into the Ubuntu VM and run the BookStack Ubuntu Installation script

> ❗ This is a script to install BookStack on a fresh instance of Ubuntu 22.04 is available. This script is ONLY FOR A FRESH OS, **it will install Apache, MySQL 8.0 & PHP-8.1 and could OVERWRITE any existing web setup on the machine**. It also does not set up mail settings or configure system security so you will have to do those separately. You can use the script as a reference if you’re installing on a non-fresh machine.

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

------





