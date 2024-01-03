# Passbolt CE - Ubuntu Server

> 🔗 [Official Passbolt CE Ubuntu Installation](https://www.passbolt.com/ce/ubuntu) (always check for updated process)
>
> 🔗 [Install Passbolt CE on Ubuntu](https://help.passbolt.com/hosting/install/ce/ubuntu/ubuntu.html)

### Network and Domain

- Point your DNS records domain to your Linux host machine, if you want to reach it from the public network.
- Open these ports so Passbolt can be accessed from inside the network (in this case).
  - `80` (http) , `11443` (https)
- On the Linux machine:

```bash
sudo ufw allow 80
sudo ufw allow 11443
sudo ufw status
```

### [Install Passbolt - Ubuntu](https://www.passbolt.com/ce/ubuntu)

```bash
wget "https://download.passbolt.com/ce/installer/passbolt-repo-setup.ce.sh"

wget "https://github.com/passbolt/passbolt-dep-scripts/releases/latest/download/passbolt-ce-SHA512SUM.txt"

sha512sum -c passbolt-ce-SHA512SUM.txt && sudo bash ./passbolt-repo-setup.ce.sh || echo "Bad checksum. Aborting" && rm -f passbolt-repo-setup.ce.sh
sudo apt install passbolt-ce-server
```

... Follow the remaining Configuration [here](https://help.passbolt.com/hosting/install/ce/ubuntu/ubuntu.html)

---

### [Docker Alternative Passbolt](https://www.passbolt.com/ce/docker)

#### Install Docker and Docker Compose

> 🔗 [Docs - Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

- Install dependencies and setup repository

```bash
# uninstall older Docker versions:
sudo apt-get remove docker docker-engine docker.io containerd runc

sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common lsb-release
```

- Install Docker Engine and Compose

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Try docker:
sudo docker compose version
sudo docker version
sudo docker run hello-world

# Configure Docker to start on boot
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

#### Install Passbolt - Docker

```bash
wget "https://download.passbolt.com/ce/docker/docker-compose-ce.yaml"
wget "https://github.com/passbolt/passbolt_docker/releases/latest/download/docker-compose-ce-SHA512SUM.txt"
sha512sum -c docker-compose-ce-SHA512SUM.txt && echo "Checksum OK" || (echo "Bad checksum. Aborting" && rm -f docker-compose-ce.yaml)
docker-compose -f docker-compose-ce.yaml up -d
```

---

