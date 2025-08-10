# BookStack

> 🔗 [BookStack Admin Documentation - Installation](https://www.bookstackapp.com/docs/admin/installation/)
>
> 🔗 [docker-bookstack](https://github.com/linuxserver/docker-bookstack)
>
> 🔗 [Ubuntu 22 Installation Script](https://www.bookstackapp.com/docs/admin/installation/#ubuntu-2204-installation-script)

- Install a Ubuntu Server VM
- Run the BookStack Ubuntu Installation script

> ❗ This is a script to install BookStack on a fresh instance of Ubuntu 22.04.
>
> This script is ONLY FOR A FRESH OS, **it will install Apache, MySQL 8.0 & PHP-8.1 and could OVERWRITE any existing web setup on the machine**. It also does not set up mail settings or configure system security so you will have to do those separately. You can use the script as a reference if you’re installing on a non-fresh machine.

```bash
# Download the script
wget https://raw.githubusercontent.com/BookStackApp/devops/main/scripts/installation-ubuntu-22.04.sh

# Make it executable
chmod a+x installation-ubuntu-22.04.sh

# Run the script with admin permissions
sudo ./installation-ubuntu-22.04.sh

# Set the VM IP as domain during the first run of BookStack
```

```bash
[1/9] Installing required system packages... (This may take several minutes)
[2/9] Preparing MySQL database...
[3/9] Downloading BookStack to /var/www/bookstack...
[4/9] Installing Composer (PHP dependency manager)...
[5/9] Installing PHP dependencies using composer...
[6/9] Creating and populating BookStack .env file...
[7/9] Running initial BookStack database migrations...
[8/9] Setting BookStack file & folder permissions...
[9/9] Configuring apache server...
----------------------------------------------------------------
Setup finished, your BookStack instance should now be installed!
```

> 📌 Default login: `admin@admin.com`:`password`

------

