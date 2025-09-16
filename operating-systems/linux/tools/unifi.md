# UniFi Network Server

---

## 🌐 Resources 🔗

>  🔗 [UniFi Installation Scripts - by Glenn R.](https://community.ui.com/questions/UniFi-Installation-Scripts-or-UniFi-Easy-Update-Script-or-UniFi-Lets-Encrypt-or-UniFi-Easy-Encrypt-/ccbc7530-dd61-40a7-82ec-22b17f027776)
>
> These scripts install/update the **UniFi Network Server** application on Ubuntu/Debian-based machines.

---

## Install UniFi Network Server

- Install an **Ubuntu Server** VM/[Proxmox LXC](https://community-scripts.github.io/ProxmoxVE/scripts?id=ubuntu) (or a supported Ubuntu/Debian-based distro) and SSH into it.
- Run the UniFi Installation Script that will **install the latest UniFi Network Server application**.

```bash
sudo sh -c '
	rm unifi-latest.sh &> /dev/null; wget https://get.glennr.nl/unifi/install/install_latest/unifi-latest.sh && bash unifi-latest.sh
'
```

- UniFi Web Page - `https://<SERVER_IP>:8443`

```bash
# Check UniFi service
sudo systemctl status unifi
sudo systemctl disable unifi
sudo systemctl enable unifi
sudo systemctl stop unifi
sudo systemctl restart unifi
```

---

## Update UniFi Network Server

```bash
sudo sh -c '
	rm unifi-update.sh &> /dev/null; wget https://get.glennr.nl/unifi/update/unifi-update.sh && bash unifi-update.sh
'
```

- Or add a `bash` alias and use it

```bash
nano $HOME/.bash_aliases
```

```bash
alias update='rm unifi-latest.sh &> /dev/null; wget https://get.glennr.nl/unifi/install/install_latest/unifi-latest.sh && bash unifi-latest.sh'

# Run the update
update
```

---

