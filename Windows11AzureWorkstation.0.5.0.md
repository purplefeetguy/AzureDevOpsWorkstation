Azure DevOps Engineer Workstation Setup on Windows 11
Purpose
A comprehensive guide to configuring a Windows 11 laptop or desktop for Azure DevOps engineering tasks, focusing on Azure Infrastructure development and operations using Infrastructure as Code (IaC) with Terraform, PowerShell, Ansible, and Kubernetes tools within a WSL2 Ubuntu environment.

Prerequisites
1. Minimum Windows 11 Version and Build for WSL2
- Windows 11 Version: 21H2 or later
- Build Number: 22000 or higher
- To check your Windows version and build, run:

winver


If your system does not meet these requirements, update Windows via Settings → Windows Update.
2. Administrator Access
- The workstation user must have local administrator privileges.
- This access is required to install software, enable features, configure WSL2, and perform system updates.
- The user should also have the ability to elevate or be granted temporary admin access for patching or troubleshooting.

3. Use of Winget and Apt for Installations
- To minimize third-party tool sprawl, this guide prefers using winget for Windows installations when possible.
- For WSL2 Ubuntu environment, use apt for software installation. Preferred sources include:- Official Ubuntu Repositories
- Snap Packages (https://snapcraft.io/)
- Official Vendor Repositories for tools like Terraform, Azure CLI, etc.


Note on Trusted Software Sources
| Tool | Official Download Source | 
| Windows Terminal | Microsoft Store / winget | 
| Visual Studio Code | https://code.visualstudio.com/ | 
| Docker Desktop | https://www.docker.com/products/docker-desktop | 
| Terraform | https://developer.hashicorp.com/terraform/downloads | 
| Ansible | https://www.ansible.com/ | 
| PowerShell | https://github.com/PowerShell/PowerShell | 
| Azure CLI | https://learn.microsoft.com/en-us/cli/azure/install-azure-cli | 



Enabling Virtualization on Windows 11 Home
- Enable Virtual Machine Platform via PowerShell:- To enable the Virtual Machine Platform on Windows 11 Home, run the following PowerShell command:
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

- Enable Windows Subsystem for Linux (WSL):- To enable WSL on Windows 11 Home, run the following command:
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

- Verify Virtualization from PowerShell:- To check if virtualization is enabled, run the following command:
systeminfo | find "Virtualization"
If virtualization is enabled, you should see "Virtualization Enabled In Firmware: Yes".


Implementing WSL2 and Configuring Ubuntu
1. Enable WSL2
Ensure the default version is WSL2:
wsl --set-default-version 2


Reboot your machine after enabling WSL2.
2. Install Ubuntu from Microsoft Store
Search for "Ubuntu" in the Microsoft Store or install via winget:
winget install -e --id Canonical.Ubuntu


Launch Ubuntu and follow the prompts to create a default user.
3. Update and Upgrade Ubuntu
Once logged in to Ubuntu:
sudo apt update && sudo apt upgrade -y


4. Verify/Install Python3 & Pip3
sudo apt install -y python3 python3-pip
python3 --version
pip3 --version



3. Install Docker in WSL2
Install Docker using Official Guide
To install Docker Desktop on your Windows 11 system, download it from Docker Desktop Download and follow the installation instructions. Ensure WSL2 Integration is enabled during the installation.
Configure Docker for Ubuntu WSL2
After installing Docker, ensure the WSL2 integration is enabled.
Start Docker in Ubuntu WSL2
Run the following commands inside your WSL2 Ubuntu environment:
sudo service docker start


Test the installation with:
docker run hello-world



4. Install Azure CLI
To install Azure CLI, run the following command inside your Ubuntu environment:
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash


Verify the installation:
az version



5. Install Terraform
To install Terraform, run the following commands inside your Ubuntu environment:
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform -y


Verify the installation:
terraform version



6. Install Bicep
To install Bicep, run:
az bicep install


Verify the installation:
az bicep version



7. Install Visual Studio Code
Download and install Visual Studio Code from VS Code Download.
Recommended Extensions
- Azure Account
- Azure CLI Tools
- Terraform
- Bicep
- Docker
- Remote - WSL


8. Final Validation Workflow
Login to Azure
az login


Initialize Terraform
terraform init


Validate Azure Provider
terraform providers


Test Resource Deployment
echo "resource \"azurerm_resource_group\" \"test\" { name = \"test-rg\" location = \"eastus\" }" > main.tf

terraform plan
terraform apply -auto-approve
terraform destroy -auto-approve


Success = Workstation Ready 🚀

Annex 1: WSL2 Configuration Differences - Windows Home vs Windows Pro
Windows 11 Home Edition
- WSL2 support is fully available on Windows 11 Home (Version 21H2 or later), but lacks Hyper-V Manager.
- Virtualization must be enabled in BIOS/UEFI.
- Uses "Virtual Machine Platform" instead of Hyper-V.
- WSL2 automatically uses the Windows Subsystem for Linux architecture without additional VM configuration.

Enable WSL2 on Home Edition:
wsl --install


Check WSL2 version:
wsl --status


Windows 11 Pro Edition
- Includes Hyper-V and advanced VM management tools.
- Full support for "Virtual Machine Platform" and "Hyper-V Platform".
- Ideal for advanced scenarios like nested virtualization, Docker Desktop with Hyper-V backend, and heavier VM workloads.

Enable WSL2 on Pro Edition:
wsl --install


(Optional) Enable Hyper-V Features:
dism.exe /online /enable-feature /featurename:Microsoft-Hyper-V-All /all /norestart


Validate WSL2 Kernel Version:
wsl --status


Summary of Differences
| Feature | Windows Home | Windows Pro | 
| Hyper-V Manager | Not Available | Available | 
| WSL2 Kernel | Supported | Supported | 
| VM Management | Basic | Advanced | 
| Ideal For | Lightweight Dev | Advanced Dev / VM Scenarios | 



Annex 2: Docker Troubleshooting in WSL2
Symptom: Docker Won't Start or Returns Strange Errors
Docker requires systemd enabled for service management inside WSL2 Ubuntu.
Solution: Enable systemd
Edit /etc/wsl.conf:
[boot]
systemd=true


Steps:
wsl -d Ubuntu
sudo nano /etc/wsl.conf
# Add [boot]\nsystemd=true
exit
wsl --shutdown
wsl
ps -p 1 -o comm=


Expected Output:
systemd


Restart Docker:
sudo service docker start


Test:
docker run hello-world




