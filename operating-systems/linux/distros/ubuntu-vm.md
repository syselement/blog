# Ubuntu - VM VirtualBox

![](.gitbook/assets/ubuntu.svg) [Ubuntu](https://ubuntu.com/)

---

## Download and Install VirtualBox

VirtualBox is a virtualization open source software that runs on Linux, Windows and Mac OS.

- Download latest **VirtualBox** and **VirtualBox Extension Pack** [here](https://www.virtualbox.org/wiki/Downloads) based on your platform (Windows in this tutorial).

![](.gitbook/assets/image-20220814155217998.png)

![](.gitbook/assets/image-20220814155334144.png)

- Install VirtualBox with default settings.
- Install VirtualBox Extension Pack.

---

## Download Linux Image

For this tutorial Ubuntu Desktop Linux will be used.

If you want to use another distro check the [Popular Linux Distributions](README.md) list.

- Download the latest **Ubuntu Desktop LTS** `.iso` file [here](https://ubuntu.com/download/desktop).

---

## Create Virtual Machine

- Run VirtualBox.
- Click on the ***New*** button to create a new virtual machine (***VM***)

![](.gitbook/assets/image-20220814160023790.png)

- Click on **Expert Mode**

![](.gitbook/assets/image-20220814163415069.png)

- Set the VM *Name*, the *machine folder* to store the VM, *type* (**`Linux`**) and *version* (**`Ubuntu 64bit`**).
- Set the Memory size (RAM) at **`4096 MB`** (4GB) or use the default settings **`1024 MB`**
- Select `Create a virtual hard disk now`
- Proceed with the ***Create*** button

![](.gitbook/assets/image-20220814162743473.png)

- Set the File size (disk size) at **`20 GB`**
- Set the Hard disk file type as **`VDI - Dynamically allocated`**
- Proceed with the ***Create*** button to initialize the machine.

![](.gitbook/assets/image-20220814164656943.png)

- Select the new VM in the manager windows and click on the ***Settings*** button.

![](.gitbook/assets/image-20220814174252593.png)

- Navigate to the ***Sistem*** tab and:

  - remove Floppy from boot order
  - set Chipset to **`PIIX3`** (default)
  - **`Enable PAE/NX`** in the Processor menu
  - check Acceleration menu to have **`Default Paravirtualization`** and **`Enable Nested Paging`** checked

![](.gitbook/assets/image-20220814174901665.png)

![](.gitbook/assets/image-20220814174955219.png)

![](.gitbook/assets/image-20220814175137011.png)

- Navigate to the ***Display*** tab and increase the **`Video Memory`** to **`128 MB`**

- **`Graphics Controller`** must be **`VMSVGA`** to be able to use VirtualBox Guest Additions

![](.gitbook/assets/image-20220814175228447.png)

---

## Install Linux Image

- Select the new VM and click on the ***Start*** button to start it

![](.gitbook/assets/image-20220814165535424.png)

- Click on the file icon to open the Optical disk selector

![](.gitbook/assets/image-20220814165746274.png)

- Use the ***Add*** button to select the `.iso` file previously downloaded, select it from the list and ***Choose*** it
- Click on the ***Start*** button to start he machine with the mounted `ubuntu-22.04-desktop-amd64.iso` (in this case)

![](.gitbook/assets/image-20220814170229539.png)

- Wait for the Ubuntu boot and installation menu
- Select your language and click ***Install Ubuntu***

![](.gitbook/assets/image-20220814171602035.png)

- Select your **Keyboard layout** and continue
- Choose **`Normal installation`** and check both ***Other options*** boxes (you must be connected to the Internet)

![](.gitbook/assets/2024-03-15_18-32-45_459.png)

- Choose **`Erase disk and install Ubuntu`** and continue the installation without enabling encryption
  - I suggest using LVM for better dynamic disk management


![](.gitbook/assets/2024-03-15_18-28-25_458.png)

![](.gitbook/assets/2024-03-15_18-33-52_460.png)

- Select your **Location** and time zone from the map screen
- Create your login details as `hostname`, `username` and `password`
- Proceed with the ***Continue*** button that will start Ubuntu installation

![](.gitbook/assets/image-20220814173136821.png)

- **Restart** your VM once the installation has completed.

![](.gitbook/assets/image-20220814174135011.png)

---

## Install VirtualBox Guest Additions

Guest Additions software unlocks some advanced features of VirtualBox to better integrate the VM and the host machine, as well as improved video support using VMSVGA graphics controller.

- Complete the VM first boot and login to Ubuntu desktop
- On the VirtualBox menu select ***Devices - Insert Guest Additions CD image...***

![](.gitbook/assets/image-20220814180203046.png)

- Run the VBox_GAs disk inside the virtual machine desktop
- **Run** the installation when prompted
  - If the prompt do not auto-start, open the disk, right-click on the **`autorun.sh`** file and ***Run as a Program***
  - Enter your password to install it

![](.gitbook/assets/image-20220814180915886.png)

![](.gitbook/assets/image-20220814181134051.png)

- Restart the VM
- Right-click on the disk and **Eject** it

![](.gitbook/assets/image-20220814181848186.png)

- Activate the *shared clipboard* from the **`Devices - Shared Clipboard - Bidirectional`** menu

![](.gitbook/assets/image-20220814181633168.png)

---

## Update Linux

Keep the Ubuntu O.S. updated using one of the next methods.

- Update it via the **Software Updater** app

![](.gitbook/assets/image-20220814182124471.png)

- Open a **Terminal** window (**`Ctrl+Alt+T`**) and update the system with the next command.
  - This can be used when connected via SSH too.

```bash
sudo apt update -y && sudo apt -y dist-upgrade
```

```bash
# Use "apt autoremove" to remove all unused packages
sudo apt -y autoremove
```

> 📌 Refer to the official documentation of the distribution for instructions on installing alternative Linux distributions.

---

## Configurations

```bash
# TIMEZONE
sudo timedatectl set-timezone Europe/Rome

# Set OS DARK MODE
gsettings set org.gnome.desktop.interface color-scheme 'prefer-dark'

# DISABLE AUTOMATIC UPDATES
sed -i 's/1";/0";/' /etc/apt/apt.conf.d/20auto-upgrades
sudo systemctl disable apt-daily{,-upgrade}.timer
sudo systemctl mask apt-daily{,-upgrade}.service

# Disable Ubuntu Pro ESM Hook and MOTD Spam - thanks to UnspamifyUbuntu
sudo mv /etc/apt/apt.conf.d/20apt-esm-hook.conf /etc/apt/apt.conf.d/20apt-esm-hook.conf.disabled
sudo sed -Ezi.orig \
  -e 's/(def _output_esm_service_status.outstream, have_esm_service, service_type.:\n)/\1    return\n/' \
  -e 's/(def _output_esm_package_alert.*?\n.*?\n.:\n)/\1    return\n/' \
  /usr/lib/update-notifier/apt_check.py
sudo /usr/lib/update-notifier/update-motd-updates-available --force

# Change "root" user password
sudo passwd root
```

> - Follow the guide here to setup `ZSH` with `Oh-My-Zsh` - [Zsh & Oh-My-Zsh - syselement](https://blog.syselement.com/home/operating-systems/linux/tools/zsh)
> - Remove unwanted spam with [UnspamifyUbuntu - Github Skyedra](https://github.com/Skyedra/UnspamifyUbuntu)

---

## Tools

### Basic Tools

```bash
# Tools
sudo apt install -y apt-transport-https aptitude btop ca-certificates curl duf gnupg htop iftop gdu locate nano ncdu neofetch net-tools nload npm pipx software-properties-common speedtest-cli sysstat terminator tree ugrep wget zsh
```

---

### [Docker - Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

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

![](.gitbook/assets/2023-06-19_23-38-34_91.png)

#### Docker commands

```bash
docker ps -a
docker exec -it <CONTAINER-ID> bash
```



#### Ubuntu Docker instance

```bash
docker run -it ubuntu bash
```

#### Wordpress Docker instance

> 🔗 Thanks to [AppSecExplained](https://gist.github.com/AppSecExplained/8bbf5366c6279ffc44beec16e6c39855) for the `yml` file.

```bash
sudo mkdir /opt/wordpress
sudo nano /opt/wordpress/docker-compose.yml
```

```bash
version: "3"
services:
  database:
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: wppassword
      MYSQL_DATABASE: wpdb
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppassword
    volumes:
      - mysql:/var/lib/mysql

  wordpress:
    depends_on:
      - database
    image: wordpress:latest
    restart: always
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: database:3306
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppassword
      WORDPRESS_DB_NAME: wpdb
    volumes:
      ["./:/var/www/html"]
volumes:
  mysql: {}
```

```bash
cd /opt/wordpress
docker compose up
```

- Open the Wordpress site

`http://localhost/wp-admin/`

- Fix `localhost` with the VM's `IP` address in the Wordpress General Settings.

![](.gitbook/assets/2023-06-19_23-49-47_92.png)

---

