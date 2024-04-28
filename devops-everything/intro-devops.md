# Introduction to DevOps

![](.gitbook/assets/devops-logo.png)

**DevOps Pipeline**

Test ➡️ Build ➡️ Deploy ➡️ Configure ➡️ Monitor

DevOps engineers have some know-how from both *DEV* and *OPERATIONs* teams, plus additional DevOps specific technical skills, the core one being building **CI/CD** (Continuous Integration / Continuous Delivery-Deployment) **pipelines**.

## Software Development

Methodologies of software development:

- Waterfall
- [Agile](https://asana.com/resources/agile-methodology) (newer)
  - several project phases
  - each feature is tested, deployed
  - fast development, testing and deployment cycles
  - Continuous Integration, Continuous Delivery
  - it supports the need for DevOps tasks (best practices, automation tools ...)

![](.gitbook/assets/agile-dev.png)



---

## 🪛 Linux Tools

> I'm using [Ubuntu Desktop LTS](https://ubuntu.com/download/desktop) as Linux distro and `zsh` as default SHELL
>
> - Additional tools can be found on my notes here ➡️ [Ubuntu VM](https://blog.syselement.com/home/operating-systems/linux/distros/ubuntu-vm#devops-tools)

> 🔗 Jump to other notes
>
> - [DevOps Resources](README.md)
> - [Git](git.md)
> - [Docker](docker.md)
> - [Kubernetes](kubernetes.md)
> - [Terraform](terraform.md)

- Package Manager - `apt` for Debian/Ubuntu-based Linux distros
- [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) (for Github, GitLab, Bitbucket)
- [java - OpenJDK](https://itsfoss.com/install-java-ubuntu/)
- [Node.js - npm](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04#option-1-installing-node-js-with-apt-from-the-default-repositories) 

```bash
sudo apt install -y git-all default-jdk nodejs npm
```

- [VScode](https://code.visualstudio.com/docs/setup/linux#_debian-and-ubuntu-based-distributions)

```bash
sudo apt update && sudo apt install -y software-properties-common apt-transport-https wget

sudo sh -c '
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /usr/share/keyrings/packages.microsoft.gpg > /dev/null
    echo "deb [arch=amd64,arm64,armhf signed-by=//usr/share/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list
    sudo apt update && sudo apt install -y code
'

EXTENSIONS=(
	ms-azuretools.vscode-docker
	streetsidesoftware.code-spell-checker \
	mhutchie.git-graph \
	esbenp.prettier-vscode \
	redhat.vscode-yaml \
	Tim-Koehler.helm-intellisense \
	oderwat.indent-rainbow \
	ms-kubernetes-tools.vscode-kubernetes-tools \
)

for EXT in "${EXTENSIONS[@]}"; do
	code --install-extension "$EXT"
done
```

- [Maven](https://maven.apache.org/install.html)
- [Gradle](https://gradle.org/install/)
- [Docker](https://docs.docker.com/engine/install/ubuntu/)

```bash
# Install Docker Engine via APT repository

for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt update -y && sudo apt install -y ca-certificates curl gnupg

sudo sh -c '
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg
    sudo chmod a+r /usr/share/keyrings/docker.gpg

    echo "deb [arch="$(dpkg --print-architecture)" signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | sudo tee /etc/apt/sources.list.d/docker.list

    sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
'

sudo systemctl enable docker --now
sudo gpasswd -a "${USER}" docker

# On Debian and Ubuntu, the Docker service starts on boot by default, if not run
sudo systemctl enable docker.service
sudo systemctl enable containerd.service

# Reboot and Test
reboot
docker run hello-world
```

- [k9s](https://k9scli.io/topics/install/) / [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management)

```bash
# Install brew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

(echo; echo "eval \"\$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)\"") >> /home/$(whoami)/.zshrc
eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"

# Install k9s
brew install derailed/k9s/k9s
brew upgrade

# Install kubectl
sudo sh -c '
	sudo apt install -y apt-transport-https ca-certificates curl
	curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | gpg --dearmor -o /usr/share/keyrings/kubernetes-apt-keyring.gpg
	echo "deb [signed-by=/usr/share/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
	sudo apt update && sudo apt install -y kubectl
'
mkdir -p $HOME/.kube
sudo touch -f $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube && chmod 700 $HOME/.kube
sudo chown $(id -u):$(id -g) $HOME/.kube/config && chmod 600 $HOME/.kube/config
```

- [minikube](https://minikube.sigs.k8s.io/docs/start/)

```bash
# Install minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
```

- [Helm](https://helm.sh/docs/intro/install/)

```bash
sudo sh -c '
    curl -fsSL https://baltocdn.com/helm/signing.asc | gpg --dearmor -o /usr/share/keyrings/helm.gpg > /dev/null
    sudo apt install -y apt-transport-https
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
    sudo apt update && sudo apt install -y helm
'
```

- [Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

```bash
# Install Terraform
sudo apt update && sudo apt install -y gnupg software-properties-common
sudo sh -c '
    wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
    https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    sudo apt update && sudo apt install -y terraform
'
```

- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-specific-operating-systems)

```bash
pipx install --include-deps ansible
pipx ensurepath
```

- [PyCharm](https://www.jetbrains.com/help/pycharm/installation-guide.html#standalone)

```bash
sudo snap install pycharm-community --classic
```

- [Azure Cli](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)

```bash
sudo sh -c '
    curl -sLS https://packages.microsoft.com/keys/microsoft.asc | sudo gpg --dearmor -o /etc/apt/keyrings/microsoft.gpg > /dev/null
    AZ_DIST=$(lsb_release -cs)
    echo "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/microsoft.gpg] https://packages.microsoft.com/repos/azure-cli/ $AZ_DIST main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    sudo apt update && sudo apt install -y azure-cli
'

# Azure kubelogin
sudo az aks install-cli
```

- [gcloud CLI](https://cloud.google.com/sdk/docs/install#deb)

[GCP gke-gcloud-auth-plugin](https://cloud.google.com/blog/products/containers-kubernetes/kubectl-auth-changes-in-gke)

```bash
# GCloud CLI, Gloud-auth-plugin
sudo sh -c '
    sudo apt install -y apt-transport-https ca-certificates gnupg curl
    curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
    echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee /etc/apt/sources.list.d/google-cloud-sdk.list
    sudo apt update && sudo apt install -y google-cloud-cli google-cloud-sdk-gke-gcloud-auth-plugin
'
```

---

## CI/CD Tools

[CI/CD](https://about.gitlab.com/topics/ci-cd/) - **Continuous Integration** / **Continuous Delivery**, pipeline

- [Github Actions](https://github.com/features/actions)
- [Gitlab CI/CD](https://docs.gitlab.com/ee/ci/)
- [Jenkins](https://www.jenkins.io/)

---

## Containerization

Containers ➡️ Image for every server, separate instances

- [Docker](https://www.docker.com/) - `Dockerfile`

---

## Orchestration

- [Kubernetes](https://kubernetes.io/) (**K8s**) - *automatic deployment, scaling, and management of containerized applications*

---

## Infrastructure Provisioning

- [Terraform](https://www.terraform.io/) - *infrastructure automation to provision and manage resources in any cloud or data center*
  - Infrastructure as Code (**IaC**)

- [Ansible](https://www.ansible.com/) - *automates provisioning, configuration management, application deployment, orchestration, and many other IT processes* - more for automation post configuration

---

## Monitoring

- [Prometheus](https://prometheus.io/) - *systems and service monitoring system*
- [Grafana](https://grafana.com/) - visualize data

---

