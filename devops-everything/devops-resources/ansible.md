# Ansible

![ansible.com](../../.gitbook/assets/ansible-logo.png)

***

## 🌐 Resources 🔗

> * [User Guide - Ansible Community Documentation](https://docs.ansible.com/ansible/latest/user_guide/index.html)
> * [jdauphant/awesome-ansible: A collaborative curated list of awesome Ansible resources](https://github.com/jdauphant/awesome-ansible)
> * [ansible-community/awesome-ansible: Awesome Ansible List](https://github.com/ansible-community/awesome-ansible)
> * [Semaphore UI](https://semaphoreui.com/)
>
> Blogs:
>
> * [An Introduction to Configuration Management with Ansible | DigitalOcean](https://www.digitalocean.com/community/conceptual-articles/an-introduction-to-configuration-management-with-ansible)
> * [How to Use Ansible: A Reference Guide | DigitalOcean](https://www.digitalocean.com/community/cheatsheets/how-to-use-ansible-cheat-sheet-guide)
> * [Ansible Tutorial for Beginners: Playbook & Examples](https://spacelift.io/blog/ansible-tutorial)

***

## [Ansible Intro](https://docs.ansible.com/ansible/latest/getting_started/introduction.html)

* **Open-source tool** for automation and configuration management
* **Agentless**: Uses SSH or WinRM for communication
* **YAML-based playbooks** for defining configurations
* **Idempotent**: Applies changes without unintended side effects
* **Modular**: Offers built-in modules for various tasks
* **Roles**: Organizes tasks for better reuse and collaboration
* **Strong community support** and extensive documentation

**Terminology**

* **Control Node**: The system running Ansible, typically on Linux/Unix
* **Managed Nodes**: Target systems controlled by Ansible, requiring SSH and Python (2.6+/3.5+). Supports various OS types, including Windows
* **Inventory**: A file listing managed hosts. Can be static (.ini) or dynamic (JSON output) for project-specific management
* **Tasks**: Units of work executed on managed nodes, either ad-hoc or within playbooks
* **Playbook**: An ordered list of tasks specifying target hosts and execution details, written in YAML
* **Handlers**: Special tasks for managing services, triggered by other tasks and run after all tasks are completed
* **Roles**: Structured collections of playbooks and files for reusable automation packages, streamlining specific deployments

***

## [Ansible Install](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible-with-pipx)

```bash
pipx install --include-deps ansible

pipx ensurepath

# pipx upgrade --include-injected ansible
```

* Generate `ansible.cfg` file

```bash
ansible-config init --disabled > ansible.cfg
```

### [Inventory](https://docs.ansible.com/ansible/latest/inventory_guide/index.html)

* Even if you do not define any groups in your inventory file, Ansible creates two default groups: `all` and `ungrouped`. The `all` group contains every host. The `ungrouped` group contains all hosts that don’t have another group aside from `all`.

```bash
sudo mkdir -p /etc/ansible
sudo nano /etc/ansible/hosts
```

```bash
[servers]
ubuntu-srv.local

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

***

## Commands

```bash
ansible --version
ansible-community --version

ansible-inventory --list -y

# module
ansible all -m ping
ansible all -m setup

# Get info about IPv4 and IPv6 network intefaces
ansible all -m setup -a "filter=*ipv*"

# ad-hoc command
ansible all -a "df -h"
ansible all -a "sudo /bin/apt update"
ansible all -a "uptime"
ansible all -a "tail /var/log/nginx/error.log" --become

# Install latest version of vim, as suoder
ansible all -m apt -a "name=vim state=latest" --become

ansible all -m apt -a "name=tree"

# install asking for password
ansible all -m apt -a "name=nginx" --become -K

ansible all -a "systemctl restart nginx" --become
```

***

## Labs

### Playbooks

```bash
mkdir -p $HOME/ansible/playbooks
cd $HOME/ansible/playbooks

nano intro_playbook.yml
```

```bash
---
- hosts: all
  become: true
  tasks:
    - name: Install Packages
      apt: name={{ item }} update_cache=yes state=latest
      loop: [ 'nginx', 'vim' ]
      tags: [ 'setup' ]

    - name: Copy index page
      copy:
        src: index.html
        dest: /var/www/html/index.html
        owner: www-data
        group: www-data
        mode: '0644'
      tags: [ 'update', 'sync' ]
```

```bash
nano index.html
```

```bash
<html>
	<head>
		<title>Testing Ansible Playbooks</title>
	</head>
	<body>
		<h1>Testing Ansible Playbooks</h1>
		<p>This server was set up using an Nginx playbook.</p>
	</body>
</html>
```

```bash
ansible-playbook ./intro_playbook.yml --list-tasks

# Execute the playbook, prompting for sudo password
ansible-playbook ./intro_playbook.yml -K -v

# This will use the current system user as remote SSH user, and the current system user’s SSH key to authenticate to the nodes.
```

***

## Semaphore UI

[Semaphore](https://github.com/semaphoreui/semaphore) is a powerful open-source tool for managing Ansible tasks through a web-based interface. This guide demonstrates how to install Semaphore on an **Ubuntu Desktop VM** using Snap (❗) and configure it for SSH-based Ansible playbooks.

> ❗ As per [Semaphore docs](https://docs.semaphoreui.com/administration-guide/installation/) Snap install is **deprecated** (but still works).
>
> * I need to do some testing to integrate [Package manager](https://docs.semaphoreui.com/administration-guide/installation/package-manager/) and/or [Docker](https://docs.semaphoreui.com/administration-guide/installation/docker/) installation documentation.

### Install via Snap (Ubuntu Desktop)

```bash
# Install Semaphore using Snap
sudo snap install semaphore

# Stop Semaphore (if needed)
sudo snap stop semaphore

# Add an admin user to Semaphore
sudo semaphore user add --admin --login user --name=User --email=user@example.com --password=P@ssw0rd!

# Start Semaphore and view its services
sudo snap start semaphore
sudo snap services semaphore

# Configure SSH for Semaphore
sudo cp $HOME/.ssh/config /root/snap/semaphore/common/sshconfig
chmod 644 /root/snap/semaphore/common/sshconfig
```

> #### Install via Docker
>
> _Not tested_
>
> Follow the steps outlined in the [official Semaphore documentation](https://github.com/semaphoreui/semaphore) for Docker-based deployment. Use the [container configurator](https://semaphoreui.com/install/docker/) to get the ideal Docker configuration for Semaphore.
>
> Here is a summary for convenience to deploy via docker with data persistence:
>
> ```bash
> docker run --name semaphore \
> -p 3000:3000 \
> -e SEMAPHORE_DB_DIALECT=bolt \
> -e SEMAPHORE_ADMIN=admin \
> -e SEMAPHORE_ADMIN_PASSWORD=changeme \
> -e SEMAPHORE_ADMIN_NAME="Admin" \
> -e SEMAPHORE_ADMIN_EMAIL=admin@localhost \
> -v semaphore_data:/var/lib/semaphore \
> -v semaphore_config:/etc/semaphore \
> -v semaphore_tmp:/tmp/semaphore \
> -d semaphoreui/semaphore:latest
> ```
>
> * [ ] Missing `.ssh/config` setup

### Post-install configuration

1. **Log In**
   * Access Semaphore at [http://localhost:3000](http://localhost:3000/)
   * Use the admin credentials set during the setup process
2. **Keystore Configuration**
   * Add the SSH keys + passphrases for secure connections
3. **Repository Configuration**
   * Link the Git repository using SSH credentials
   * **e.g.** - `git@github.com:syselement/ansibletest.git`
4. **Environment**
   * Set up an empty environment
5. **Static Inventory Configuration**
   * Define an inventory file with host and group details
   * **e.g.**

```bash
[pihole]
pihole.lan.syselement.com

[ubuntu]
ubuntu-srv.lan.syselement.com

[all:vars]
ansible_ssh_common_args = '-F /root/snap/semaphore/common/sshconfig'
```

### Creating Ansible Task Templates

Task templates in Semaphore point to Ansible playbooks stored in the repository. Here’s how to set them up:

1. **Organize the repository**
   * Structure the repository to store playbooks logically.
   * **e.g.**

```bash
ansible/
├── playbooks/
│   ├── updatelinux.yaml
│   ├── deployweb.yaml
└── roles/
    ├── common/
    ├── webserver/

```

2. **Create a template in Semaphore**
   * Add a new **Task Template** in Semaphore
     * **e.g.**
     * Name - `AptUpdateUbuntu`
       * Playbook Path - Relative path to the playbook - `ansible/playbooks/updatelinux.yaml`

***
