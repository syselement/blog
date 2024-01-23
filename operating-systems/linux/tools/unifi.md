# Ubiquiti UniFi

>  🔗 [UniFi Installation Scripts - by Glenn R.](https://community.ui.com/questions/UniFi-Installation-Scripts-or-UniFi-Easy-Update-Script-or-UniFi-Lets-Encrypt-or-UniFi-Easy-Encrypt-/ccbc7530-dd61-40a7-82ec-22b17f027776)
>
> These scripts install/update the **UniFi Network application** on Ubuntu/Debian-based machines.

## Install UniFi

- Install a Ubuntu Server VM (or a supported Ubuntu/Debian-based distro) and SSH into it if necessary.
- Run the UniFi Installation Script that will *install the latest UniFi Network application.*

```bash
sudo su
rm unifi-latest.sh &> /dev/null; wget https://get.glennr.nl/unifi/install/install_latest/unifi-latest.sh && bash unifi-latest.sh
```

- UniFi Web Page [https://localhost:8443/](https://localhost:8443/)

```bash
# Check UniFi service
sudo systemctl status unifi
sudo systemctl disable unifi
sudo systemctl enable unifi
sudo systemctl stop unifi
sudo systemctl restart unifi
```

## Update UniFi

```bash
sudo su
rm unifi-update.sh &> /dev/null; wget https://get.glennr.nl/unifi/update/unifi-update.sh && bash unifi-update.sh
```



------

## 