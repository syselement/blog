# Nessus Essentials

> [Nessus](https://docs.tenable.com/Nessus.htm)
>
> [NessusCLI](https://docs.tenable.com/nessus/Content/NessusCLI.htm)

## Install Nessus

* Links
  * [Nessus Linux Install](https://docs.tenable.com/nessus/Content/InstallNessusLinux.htm#Download-the-Nessus-package-file.)
  * [Nessus Downloads](https://www.tenable.com/downloads/nessus)

- Download Ubuntu version by `curl` (command created from the Downloads site)

```bash
curl --request GET \
  --url 'https://www.tenable.com/downloads/api/v2/pages/nessus/files/Nessus-10.5.0-ubuntu1404_amd64.deb' \
  --output 'Nessus-10.5.0-ubuntu1404_amd64.deb'
```

```bash
sudo dpkg -i Nessus-<version number>-debian6_amd64.deb

sudo systemctl start nessusd.service

# Stop & Update Nessus:
sudo systemctl stop nessusd.service
sudo /opt/nessus/sbin/nessuscli update --all
```

- Open Nessus in a local browser
  - `https://127.0.0.1:8834/`

## Configure Nessus

- Links
  - [Install Nessus Essentials, Professional, Expert, or Manager](https://docs.tenable.com/nessus/Content/InstallNessusEssentialsProfessionalOrManager.htm)

- **Register for Nessus Essentials** to install Nessus Essentials
- **Get an activation** code 
  - On the **Get an activation code** screen, type your name and email address.
  - Click **Email**.
  - Check your email for your free activation code.
- On the **Register Nessus** screen, type your **Activation Code**.
- Proceed with the creation of a **Nessus administrator** user account.

## Reset Nessus

### Soft

- *This command deletes all your registration information and preferences, causing Nessus to run in a non-registered state. Nessus Manager retains the same linking key after resetting.*

```bash
sudo /opt/nessus/sbin/nessuscli fix --reset
```

### Hard

- *This command resets Nessus to a fresh state, deleting all registration information, settings, data, and users.*

```bash
sudo /opt/nessus/sbin/nessuscli fix --reset-all
```

------

