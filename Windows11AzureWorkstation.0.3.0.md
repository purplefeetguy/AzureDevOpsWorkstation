# Azure DevOps Engineer Workstation Setup on Windows 11

## Purpose
A comprehensive guide to configuring a Windows 11 laptop or desktop for Azure DevOps engineering tasks, focusing on Azure Infrastructure development and operations using Infrastructure as Code (IaC) with Terraform, PowerShell, Ansible, and Kubernetes tools within a WSL2 Ubuntu environment.

---

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
- To minimize third-party tool sprawl, this guide prefers using `winget` for Windows installations when possible.
- For WSL2 Ubuntu environment, use `apt` for software installation. Preferred sources include:
  - Official Ubuntu Repositories
  - Snap Packages (https://snapcraft.io/)
  - Official Vendor Repositories for tools like Terraform, Azure CLI, etc.

#### Note on Trusted Software Sources
| Tool | Official Download Source |
|------|---------------------------|
| Windows Terminal | Microsoft Store / winget |
| Visual Studio Code | https://code.visualstudio.com/ |
| Docker Desktop | https://www.docker.com/products/docker-desktop |
| Terraform | https://developer.hashicorp.com/terraform/downloads |
| Ansible | https://www.ansible.com/ |
| PowerShell | https://github.com/PowerShell/PowerShell |
| Azure CLI | https://learn.microsoft.com/en-us/cli/azure/install-azure-cli |

---

## Implementing WSL2 and Configuring Ubuntu

### 1. Enable WSL2

```powershell
wsl --install
```

Ensure the default version is WSL2:

```powershell
wsl --set-default-version 2
```

Reboot your machine after enabling WSL2.

### 2. Install Ubuntu from Microsoft Store
Search for "Ubuntu" in the Microsoft Store or install via winget:

```powershell
winget install -e --id Canonical.Ubuntu
```

Launch Ubuntu and follow the prompts to create a default user.

### 3. Update Ubuntu Software
Once logged in to Ubuntu:

```bash
sudo apt update && sudo apt upgrade -y
```

### 4. Verify/Install Python3 & Pip3

```bash
sudo apt install -y python3 python3-pip
python3 --version
pip3 --version
```

### 5. Install Docker Engine in WSL2

#### Add Docker's Official Repository

```bash
sudo apt install -y ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

#### Test Docker

```bash
sudo service docker start
sudo docker run hello-world
```

#### Add Current User to Docker Group
To avoid needing `sudo` for every Docker command:

```bash
sudo usermod -aG docker $USER
```

Then either log out and back in, or run:

```bash
newgrp docker
```

Test Docker without sudo:

```bash
docker run hello-world
```

---

## Installing Visual Studio Code

### Purpose
VS Code is a lightweight but powerful source code editor that runs on Windows, macOS, and Linux. It is essential for Azure DevOps engineering tasks due to its extensive extension library and native integration with Azure tooling.

### Installation Steps

#### 1. Install Visual Studio Code via Winget
```powershell
winget install --id Microsoft.VisualStudioCode --source winget
```

#### 2. Verify Installation
Launch VS Code from Start Menu or run:
```powershell
code
```

### Recommended VS Code Extensions for Azure DevOps Engineering

| Extension | Purpose |
|-----------|---------|
| Azure Account | Authenticate and manage Azure subscriptions |
| Azure CLI Tools | Syntax highlighting and IntelliSense for Azure CLI |
| Azure Terraform | Terraform tooling support for Azure |
| HashiCorp Terraform | Terraform syntax, linting, formatting, and validation |
| PowerShell | PowerShell language support with IntelliSense |
| Remote - WSL | Seamless coding inside WSL2 environments |
| YAML | YAML language support |
| Docker | Docker tooling integration |
| Kubernetes | Kubernetes object exploration and manifest authoring |

### Install Recommended Extensions Automatically
Use the following command to install all recommended extensions:

```powershell
code --install-extension ms-vscode.azure-account
code --install-extension ms-vscode.azurecli
code --install-extension ms-azuretools.vscode-azureterraform
code --install-extension hashicorp.terraform
code --install-extension ms-vscode.powershell
code --install-extension ms-vscode-remote.remote-wsl
code --install-extension redhat.vscode-yaml
code --install-extension ms-azuretools.vscode-docker
code --install-extension ms-kubernetes-tools.vscode-kubernetes-tools
```

### Validate VS Code + Extension Setup

1. Launch VS Code:
```powershell
code
```

2. Confirm extensions are installed:
- Navigate to Extensions sidebar (Ctrl+Shift+X)
- Validate listed extensions are installed and enabled

3. Test WSL2 Remote Integration:
- Open VS Code
- Select "Remote Explorer" → Connect to WSL
- Open a WSL terminal and confirm it is working

4. Test Terraform Linting:
- Create a `.tf` file and verify syntax highlighting and linting are working

5. Test PowerShell Integration:
- Open a `.ps1` file and confirm PowerShell IntelliSense works

6. Test Azure Authentication:
- Open Command Palette (Ctrl+Shift+P)
- Search for `Azure: Sign In`
- Authenticate successfully

---

## Annex 1: WSL2 Configuration Differences - Windows Home vs Windows Pro

### Windows 11 Home Edition
- WSL2 support is fully available on Windows 11 Home (Version 21H2 or later), but lacks Hyper-V Manager.
- Virtualization must be enabled in BIOS/UEFI.
- Uses "Virtual Machine Platform" instead of Hyper-V.
- WSL2 automatically uses the Windows Subsystem for Linux architecture without additional VM configuration.

Enable WSL2 on Home Edition:
```powershell
wsl --install
```

Check WSL2 version:
```powershell
wsl --status
```

### Windows 11 Pro Edition
- Includes Hyper-V and advanced VM management tools.
- Full support for "Virtual Machine Platform" and "Hyper-V Platform".
- Ideal for advanced scenarios like nested virtualization, Docker Desktop with Hyper-V backend, and heavier VM workloads.

Enable WSL2 on Pro Edition:
```powershell
wsl --install
```

(Optional) Enable Hyper-V Features:
```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Hyper-V-All /all /norestart
```

Validate WSL2 Kernel Version:
```powershell
wsl --status
```

### Summary of Differences
| Feature | Windows Home | Windows Pro |
|---------|----------------|--------------|
| Hyper-V Manager | Not Available | Available |
| WSL2 Kernel | Supported | Supported |
| VM Management | Basic | Advanced |
| Ideal For | Lightweight Dev | Advanced Dev / VM Scenarios |

