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

---