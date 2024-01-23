# SysReptor

> 🔗 [SysReptor Pentest Report Creator](https://docs.sysreptor.com/)
>
> 🔗 Local install: [http://127.0.0.1:8000/](http://127.0.0.1:8000/)

## Install SysReptor

```bash
sudo apt update
sudo apt install sed curl openssl uuid-runtime coreutils

# Community
cd ~/tools
curl -s https://docs.sysreptor.com/install.sh | bash

# The installation script creates a new sysreptor directory holding the source code and everything you need. It will build a docker image, create volumes and secrets and bring up your containers.
```

## Update SysReptor

```bash
# Update
bash ~/tools/sysreptor/update.sh
```

```bash
# Restart
cd ~/tools/sysreptor/deploy
docker compose -f docker-compose.yml up -d
```

## Configure

```bash
# HTB Designs/Templates
cd ~/tools/sysreptor/deploy
url="https://docs.sysreptor.com/assets/htb-designs.tar.gz"
curl -s "$url" | docker compose exec --no-TTY app python3 manage.py importdemodata --type=design
```

> ➡️ Check the official [documentation](https://docs.sysreptor.com/setup/configuration/) for further configuration and usage.

---

