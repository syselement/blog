# [Ansible](https://www.ansible.com/)

![ansible.com](.gitbook/assets/ansible-logo.png)

---

## 🌐 Resources 🔗

> - [User Guide - Ansible Community Documentation](https://docs.ansible.com/ansible/latest/user_guide/index.html)
> - [jdauphant/awesome-ansible: A collaborative curated list of awesome Ansible resources](https://github.com/jdauphant/awesome-ansible)
> - [ansible-community/awesome-ansible: Awesome Ansible List](https://github.com/ansible-community/awesome-ansible)
> - [Semaphore UI](https://semaphoreui.com/)
>
> Blogs:
>
> - [An Introduction to Configuration Management with Ansible | DigitalOcean](https://www.digitalocean.com/community/conceptual-articles/an-introduction-to-configuration-management-with-ansible)
> - [How to Use Ansible: A Reference Guide | DigitalOcean](https://www.digitalocean.com/community/cheatsheets/how-to-use-ansible-cheat-sheet-guide)
> - [Ansible Tutorial for Beginners: Playbook & Examples](https://spacelift.io/blog/ansible-tutorial)

---

## [Ansible Intro](https://docs.ansible.com/ansible/latest/getting_started/introduction.html)

- **Open-source tool** for automation and configuration management
- **Agentless**: Uses SSH or WinRM for communication
- **YAML-based playbooks** for defining configurations
- **Idempotent**: Applies changes without unintended side effects
- **Modular**: Offers built-in modules for various tasks
- **Roles**: Organizes tasks for better reuse and collaboration
- **Strong community support** and extensive documentation



**Terminology**

- **Control Node**: The system running Ansible, typically on Linux/Unix
- **Managed Nodes**: Target systems controlled by Ansible, requiring SSH and Python (2.6+/3.5+). Supports various OS types, including Windows
- **Inventory**: A file listing managed hosts. Can be static (.ini) or dynamic (JSON output) for project-specific management
- **Tasks**: Units of work executed on managed nodes, either ad-hoc or within playbooks
- **Playbook**: An ordered list of tasks specifying target hosts and execution details, written in YAML
- **Handlers**: Special tasks for managing services, triggered by other tasks and run after all tasks are completed
- **Roles**: Structured collections of playbooks and files for reusable automation packages, streamlining specific deployments

---

## [Ansible Install](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible-with-pipx)

```bash
pipx install --include-deps ansible

pipx ensurepath

# pipx upgrade --include-injected ansible
```

- Generate `ansible.cfg` file

```bash
ansible-config init --disabled > ansible.cfg
```

### [Inventory](https://docs.ansible.com/ansible/latest/inventory_guide/index.html)

- Even if you do not define any groups in your inventory file, Ansible creates two default groups: `all` and `ungrouped`. The `all` group contains every host. The `ungrouped` group contains all hosts that don’t have another group aside from `all`.

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

---

