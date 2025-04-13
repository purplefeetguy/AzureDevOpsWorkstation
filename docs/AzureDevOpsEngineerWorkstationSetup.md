Azure DevOps Engineer Workstation Setup on Windows 11

## Purpose

A comprehensive guide to configuring a Windows 11 laptop or desktop for Azure DevOps engineering tasks, focusing on Azure Infrastructure development and operations using Infrastructure as Code (IaC) with Terraform, PowerShell, Ansible, and Kubernetes tools within a WSL2 Ubuntu environment.

## Prerequisites

### 1. Minimum Windows 11 Version and Build for WSL2

- Windows 11 Version: 21H2 or later
- Build Number: 22000 or higher
- To check your Windows version and build, run:
  ```powershell
  winver
  ```

If your system does not meet these requirements, update Windows via Settings → Windows Update.

### 2. Administrator Access

- The workstation user must have local administrator privileges.
- This access is required to install software, enable features, configure WSL2, and perform system updates.
- The user should also have the ability to elevate or be granted temporary admin access for patching or troubleshooting.

### 3. Use of Winget and Apt for Installations

- To minimize third-party tool sprawl, this guide prefers using winget for Windows installations when possible.
- For WSL2 Ubuntu environment, use apt for software installation.
  - Official Ubuntu Repositories
  - Snap Packages ([https://snapcraft.io/](https://snapcraft.io/))
  - Official Vendor Repositories for tools like Terraform, Azure CLI, etc.

### Note on Trusted Software Sources

| Tool               | Official Download Source                                                                                                       |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| Windows Terminal   | Microsoft Store / winget                                                                                                       |
| Visual Studio Code | [https://code.visualstudio.com/](https://code.visualstudio.com/)                                                               |
| Docker Desktop     | [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)                               |
| Terraform          | [https://developer.hashicorp.com/terraform/downloads](https://developer.hashicorp.com/terraform/downloads)                     |
| Ansible            | [https://www.ansible.com/](https://www.ansible.com/)                                                                           |
| PowerShell         | [https://github.com/PowerShell/PowerShell](https://github.com/PowerShell/PowerShell)                                           |
| Azure CLI          | [https://learn.microsoft.com/en-us/cli/azure/install-azure-cli](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) |

## Enabling Virtualization on Windows 11 Home

### Enable Virtual Machine Platform via PowerShell

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

### Enable Windows Subsystem for Linux (WSL)

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

### Verify Virtualization

```powershell
systeminfo | find "Virtualization"
```

## Implementing WSL2 and Configuring Ubuntu

### 1. Enable WSL2

```bash
wsl --set-default-version 2
```

Reboot your machine after enabling WSL2.

### 2. Install Ubuntu from Microsoft Store

```powershell
winget install -e --id Canonical.Ubuntu
```

Launch Ubuntu and follow the prompts to create a default user.

### 3. Update and Upgrade Ubuntu

```bash
sudo apt update && sudo apt upgrade -y
```

### 4. Verify/Install Python3 & Pip3

```bash
sudo apt install -y python3 python3-pip
python3 --version
pip3 --version
```

### 5. Install Docker in WSL2

#### Install Docker using Official Guide

Download Docker Desktop from the [Docker Desktop site](https://www.docker.com/products/docker-desktop). Enable WSL2 integration during the installation.

#### Configure Docker for Ubuntu WSL2

Run in WSL2 Ubuntu:

```bash
sudo service docker start
docker run hello-world
```

### 6. Install Azure CLI

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az version
```

### 7. Install Terraform

```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform -y
terraform version
```

### 8. Install Bicep

```bash
az bicep install
az bicep version
```

### 9. Install Visual Studio Code

Install from [https://code.visualstudio.com/](https://code.visualstudio.com/)

#### Recommended Extensions

- Azure Account
- Azure CLI Tools
- Terraform
- Bicep
- Docker
- Remote - WSL

## Final Validation Workflow

### Login to Azure

```bash
az login
```

### Initialize Terraform

```bash
terraform init
```

### Validate Azure Provider

```bash
terraform providers
```

### Test Resource Deployment

```hcl
echo "resource \"azurerm_resource_group\" \"test\" { name = \"test-rg\" location = \"eastus\" }" > main.tf
terraform plan
terraform apply -auto-approve
terraform destroy -auto-approve
```

✅ Success = Workstation Ready 🚀

---

## Annex 1: WSL2 Configuration Differences - Windows Home vs Windows Pro

### Windows 11 Home Edition

- No Hyper-V Manager
- Uses Virtual Machine Platform
- Enable via:
  ```bash
  wsl --install
  wsl --status
  ```

### Windows 11 Pro Edition

- Includes Hyper-V
- Enable with:
  ```powershell
  dism.exe /online /enable-feature /featurename:Microsoft-Hyper-V-All /all /norestart
  ```

| Feature         | Windows Home    | Windows Pro                 |
| --------------- | --------------- | --------------------------- |
| Hyper-V Manager | ❌               | ✅                           |
| WSL2 Kernel     | ✅               | ✅                           |
| VM Management   | Basic           | Advanced                    |
| Ideal For       | Lightweight Dev | Advanced Dev / VM Scenarios |

---

## Annex 2: Docker Troubleshooting in WSL2

### Enable systemd in WSL2

Edit `/etc/wsl.conf`:

```ini
[boot]
systemd=true
```

Restart WSL:

```bash
wsl --shutdown
wsl
ps -p 1 -o comm=
```

Expected: `systemd`

### Restart Docker

```bash
sudo service docker start
docker run hello-world
```

---

## Annex 3: Kubernetes + Networking Setup in WSL2

### 1. Hyper-V Firewall Settings

```powershell
Set-NetFirewallHyperVVMSetting -Name '{VM-ID}' -DefaultInboundAction Allow
```

### 2. Docker Desktop K8s Context

```bash
kubectl config get-contexts
kubectl config use-context docker-desktop
```

### 3. Minikube Cluster

```bash
kubectl config use-context minikube
kubectl get nodes
```

### 4. Minikube Network Troubleshooting

```bash
minikube service list
```

Ensure NodePort access by adjusting firewall and exposing correct ports.

### 5. DNS Tunneling

Edit `.wslconfig`:

```ini
[wsl2]
dnsTunneling=true
```

### 6. Auto Proxy

```ini
[wsl2]
autoProxy=true
```

### 7. Kubeconfig Management

```bash
kubectl config view --raw > ~/.kube/config
```

Merge and manage contexts for `docker-desktop` and `minikube`.

### 8. Final Network Testing

```bash
kubectl get services
curl http://127.0.0.1:<port>
```



# Azure DevOps Engineer Workstation Setup

> ⚙️ Dev Mode Edition — with full port access, DNS tunneling, and proxy hacks included

---

## Changelog

- **2025-04-13 (patch):** Renamed setup doc, removed vestigial versioning, added GitHub version control notes
- **2025-04-13 (patch):** Added section plans for Hyper-V firewall, DNS tunneling, and autoProxy

---

## 1. Prerequisites

- Windows 11 Pro with WSL2 enabled
- Ubuntu 22.04 (via WSL)
- Hyper-V and virtualization enabled in BIOS
- Admin access

---

## 2. Tooling Versions

| Tool              | Version      |
|-------------------|--------------|
| Azure CLI         | 2.59+        |
| Terraform         | 1.6.x        |
| Git               | Latest       |
| kubectl           | 1.29.x       |
| Helm              | 3.14.x       |
| Minikube          | 1.33+        |
| Docker CLI        | 24.x         |
| Azure Dev CLI     | Latest       |
| Visual Studio Code| Stable + Extensions |

---

## 3. GitHub Setup for Version Control

```bash
mkdir -p ~/dev/azure-workstation-setup
cd ~/dev/azure-workstation-setup
git init
git remote add origin git@github.com:<your-username>/azure-workstation-setup.git
echo "# Azure DevOps Workstation Setup" > README.md
git add .
git commit -m "init"
git push -u origin main
```

> 📁 Put your `docs/`, `.azure/`, and `.terraform/` examples here.

---

## 4. VS Code Setup

### Extensions
- Azure Tools
- Terraform
- Kubernetes
- YAML
- Remote - WSL
- GitLens

### Validation
```bash
code --version
code-insiders --list-extensions
```

> 🎨 Customize `settings.json` for formatting, linting, and IntelliSense.

---

## 5. WSL2 Ubuntu Configuration

### Locale and Timezone
```bash
sudo dpkg-reconfigure tzdata
sudo locale-gen en_US.UTF-8
```

### Base Packages
```bash
sudo apt update && sudo apt install -y \
  build-essential curl wget unzip jq git \
  dnsutils net-tools gnupg2 lsb-release apt-transport-https
```

> 🚀 You're now ready to install cloud tooling.

---

## 6. Azure CLI + Terraform Setup

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az version

# Terraform
wget https://releases.hashicorp.com/terraform/1.6.6/terraform_1.6.6_linux_amd64.zip
unzip terraform_1.6.6_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform version
```

> 🧪 Test by authenticating: `az login` and `terraform init`

---

## 7. Kubernetes + Minikube Setup (Coming Up)

> 🛠️ We'll dive into this after network setup.

---

## 8. Network Configuration for Dev Mode 🛠️

### Hyper-V Firewall Rules (Wide-Open Dev Mode)

During development, especially with Kubernetes or service mesh workloads, you might need to open up wide ranges of ports temporarily. Here's how to configure this on Windows with Hyper-V-enabled environments:

```powershell
# Open NodePort range for Kubernetes services (30000–32767)
New-NetFirewallRule -DisplayName "K8s NodePort Range" -Direction Inbound -LocalPort 30000-32767 -Protocol TCP -Action Allow

# Optional: Allow all TCP/UDP during dev mode (use with caution)
New-NetFirewallRule -DisplayName "DevMode All TCP" -Direction Inbound -Protocol TCP -Action Allow
New-NetFirewallRule -DisplayName "DevMode All UDP" -Direction Inbound -Protocol UDP -Action Allow
```

> 💡 Be sure to remove these rules before moving to production-like setups.

### DNS Tunneling for Custom Resolution

If you're routing DNS manually (e.g., for local clusters or custom domains via `nip.io` or similar), consider running a local DNS proxy with CoreDNS or Dnsmasq:

**WSL2 Setup Example:**

```bash
sudo apt update && sudo apt install -y dnsmasq

# /etc/dnsmasq.conf
address=/test.local/127.0.0.1
listen-address=127.0.0.1
```

Then configure WSL to use it:

```bash
echo "nameserver 127.0.0.1" | sudo tee /etc/resolv.conf > /dev/null
sudo chattr +i /etc/resolv.conf  # Prevent overwrites
```

### autoProxy Configuration for CLI Tools

For tools like `az`, `terraform`, `kubectl`, or even package managers, set up automatic proxy behavior based on environment:

```bash
# ~/.bashrc or ~/.zshrc
export http_proxy=http://127.0.0.1:8888
export https_proxy=http://127.0.0.1:8888
export no_proxy=localhost,127.0.0.1,.svc,.local

# Optional: wrap CLI tools to detect when proxy is down
alias az='PROXY_ON=1 az'
```

> ✅ Validate with `env | grep proxy` and curl to ensure routing works.

---

Next up: **Minikube setup in Hyper-V with custom DNS and port routing**



Certainly! Here's the **Minikube Setup in Docker** section in full markdown format:

```markdown
## 4. Minikube Setup in Docker

To set up **Minikube** in Docker, follow these steps:

### 1. Install Minikube in Docker

First, install Minikube in your WSL2 environment using Docker as the driver:

```bash
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube
sudo mv minikube /usr/local/bin
```

### 2. Start Minikube with Docker Driver

Start Minikube using Docker as the driver:

```bash
minikube start --driver=docker
```

This command will initiate a Minikube cluster using Docker as the VM driver for your Kubernetes environment.

### 3. Verify the Minikube Cluster

After Minikube starts, you can verify the cluster's status using:

```bash
minikube status
```

This will give you the current status of your Minikube cluster, including information about the Docker container running your Kubernetes setup.

### 4. Accessing Kubernetes with kubectl

To access the Kubernetes cluster, you will need to use `kubectl`. Minikube configures `kubectl` automatically for you. You can verify your connection by running:

```bash
kubectl cluster-info
```

This will show details about the Kubernetes control plane and the cluster configuration.

### 5. Stopping Minikube

To stop your Minikube cluster, run:

```bash
minikube stop
```

This command stops the Minikube VM (or Docker container in this case) without deleting the cluster.

### 6. Deleting the Minikube Cluster

If you wish to delete the Minikube cluster, run:

```bash
minikube delete
```

This will completely remove the Minikube environment, including the Docker container and Kubernetes configuration.

```

This markdown provides the entire process for setting up Minikube using Docker as the driver. Let me know if this works for you!
---