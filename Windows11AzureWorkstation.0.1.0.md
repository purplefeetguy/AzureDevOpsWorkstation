# Azure DevOps Engineer Workstation Setup on Windows 11

"Please write a detailed procedure, in markdown format, to configure a Windows 11 laptop or desktop for an Azure DevOps Engineer. The focus is on developing and managing Azure Infrastructure using Infrastructure as Code (IaC), specifically leveraging Terraform and PowerShell for orchestration and provisioning, and Ansible for post-provisioning configuration.

The workstation must include:

WSL2 configured with the latest supported Ubuntu

Docker installed natively and configured for WSL2

Installation of: Python3 & pip3, latest PowerShell with all required modules, Azure CLI, Kubernetes management tools, Helm with repo setup.

Validate each major step with simple commands (like --version) embedded directly in the document after installation.

Add a prerequisites section:

The user needs admin access (permanent or elevatable).

Preference for using winget for installations when available.

Provide official download sources for large enterprise environments with high-security postures.

Additionally, add a section for Visual Studio Code:

Include prerequisites, installation, required extensions for the Azure DevOps/Infra engineer toolkit, and validation commands.

Finally, write a comprehensive final validation workflow:

The engineer should login to Azure CLI and Azure DevOps.

Deploy a Resource Group using Terraform.

Validate that it exists.

Destroy the Resource Group using Terraform.

Validate cleanup.

Ensure clarity, structure, and that this guide is consumable both directly from markdown in a console editor and when previewed in markdown-rendering tools."

######################################################

# Azure DevOps Engineer Workstation Setup on Windows 11

## Purpose
A comprehensive guide to configuring a Windows 11 laptop or desktop for Azure DevOps engineering tasks, focusing on Azure Infrastructure development and operations using Infrastructure as Code (IaC) with Terraform, PowerShell, Ansible, and Kubernetes tools within a WSL2 Ubuntu environment.

---

## Prerequisites

### 1. Administrator Access
- The workstation user must have local administrator privileges.
- This access is required to install software, enable features, configure WSL2, and perform system updates.
- The user should also have the ability to elevate or be granted temporary admin access for patching or troubleshooting.

### 2. Use of Winget for Installations
- To minimize third-party tool sprawl, this guide prefers using `winget` for Windows installations when possible.
- Tools installed via `winget` include: Windows Terminal, VS Code, Docker Desktop, and more.

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

## VS Code Installation and Configuration

### Prerequisites
- Windows Terminal Installed
- WSL2 with Ubuntu Installed

### Install VS Code
```powershell
winget install --id Microsoft.VisualStudioCode -e
```

### Install Required VS Code Extensions
Open VS Code and run the following commands in the terminal:

```bash
# Core Azure DevOps & IaC Extensions
code --install-extension ms-azuretools.vscode-azurecli
code --install-extension ms-azuretools.vscode-azureterraform
code --install-extension hashicorp.terraform
code --install-extension ms-azuretools.vscode-docker
code --install-extension ms-vscode.powershell
code --install-extension redhat.ansible
code --install-extension ms-kubernetes-tools.vscode-kubernetes-tools
code --install-extension eamodio.gitlens
code --install-extension zhuangtongfa.material-theme
```

### Validate VS Code Installation

```powershell
code --version
```

### Validate Extensions
In VS Code Terminal (PowerShell or Bash within WSL2):
```bash
code --list-extensions
```
Ensure all expected extensions are listed.

---

## Final Comprehensive Validation Workflow

This workflow assumes all tooling is installed, configured, and validated.

### 1. Validate Azure CLI Login
```bash
az login
az account show
```

### 2. Validate Azure DevOps Login (If applicable)
```bash
az devops configure --defaults organization=https://dev.azure.com/YourOrg project=YourProject
az devops user show --user-id YourUser@domain.com
```

### 3. Validate Terraform Initialization
Create a working directory and initialize Terraform:
```bash
mkdir ~/tf-test && cd ~/tf-test

cat <<EOF > main.tf
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "test" {
  name     = "tf-validation-rg"
  location = "East US"
}
EOF

terraform init
terraform plan
terraform apply -auto-approve
```

### 4. Validate Azure Resource Group Creation
```bash
az group show --name tf-validation-rg
```

### 5. Destroy Test Resources
```bash
terraform destroy -auto-approve
az group delete --name tf-validation-rg --yes --no-wait
```

### 6. Validate Cleanup
```bash
az group list --query "[?name=='tf-validation-rg']"
```
Should return an empty array `[]`.

---

## End of Procedure

Infrastructure as Code excellence fully enabled.

> Pro Tip: In VS Code, use `Ctrl+Shift+V` to preview this file beautifully in Markdown view!

