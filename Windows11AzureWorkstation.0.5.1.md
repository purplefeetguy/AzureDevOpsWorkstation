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


### integrate the details below into the document above

Here’s a detailed summary of everything we’ve worked on over the last 10 hours, including the steps taken, commands used, and explanations for each change:

---

### 1. **Hyper-V Firewall Settings and Configuration**

**Objective**: Adjust the firewall settings for Hyper-V to allow inbound connections for WSL2-based services and containers.

- **Initial State**: Hyper-V default firewall settings were blocking inbound connections.
  
**Steps Taken**:
- We ran the following command to enable inbound connections on Hyper-V VM interfaces:
  ```powershell
  Set-NetFirewallHyperVVMSetting -Name '{40E0AC32-46A5-438A-A0B2-2B479E8F2E90}' -DefaultInboundAction Allow
  ```
  This change was made to ensure WSL2 could accept traffic from external sources.

- **Why**: This allows WSL2-based Kubernetes or Docker services to be accessed from the Windows host, necessary for testing services and running containers.

---

### 2. **Docker Desktop Configuration for Kubernetes**

**Objective**: Ensure that Docker Desktop was correctly configured to allow Kubernetes and services running within Docker Desktop to be accessible from WSL2.

- **Command** to verify Docker Desktop context:
  ```bash
  kubectl config get-contexts
  ```

- **Reason**: To ensure that we had the correct context for interacting with Kubernetes in Docker Desktop, especially when switching contexts between Minikube and Docker.

- **Outcome**: We successfully ensured the context was set to `docker-desktop`, allowing Kubernetes in Docker Desktop to be accessed via `kubectl`.

---

### 3. **Minikube Configuration and Testing**

**Objective**: Set up and test a Minikube cluster in WSL2 for local Kubernetes workloads.

- **Command** to set Minikube's context:
  ```bash
  kubectl config use-context minikube
  ```

- **Command** to check nodes in Minikube:
  ```bash
  kubectl get nodes
  ```

- **Issue**: The `kubectl` command was failing with connection errors, likely due to network issues related to the VM used by Minikube (inside WSL2).

- **Fix**: We investigated and ultimately needed to update the kubeconfig file with the correct context after network-related changes in Hyper-V and Docker.

---

### 4. **Minikube Network and Connectivity Issues**

**Objective**: Ensure connectivity between WSL2 (Ubuntu) and the Kubernetes services exposed through Minikube.

- **Issue**: Minikube was unable to establish connections to services, even though they were exposed.

- **Actions Taken**:
  - We verified the status of services running with:
    ```bash
    minikube service list
    ```
  - Checked port forwarding configurations to ensure traffic routing.
  - **Discovered** that Minikube uses its own IP address and exposes services on specific ports, requiring us to open those ports on Windows and WSL2.

---

### 5. **DNS Tunneling for WSL2**

**Objective**: Set up DNS tunneling to enable WSL2 to bypass certain network restrictions and improve compatibility with services (e.g., VPNs).

- **Command** to enable DNS tunneling in `.wslconfig`:
  ```ini
  [wsl2]
  dnsTunneling=true
  ```

- **Why**: Enabling DNS tunneling ensures WSL2 uses a more compatible DNS configuration when running on networks with complex setups, like VPNs. This helps prevent issues where DNS queries fail within WSL2 when certain networking restrictions exist.

---

### 6. **Auto Proxy Setup in WSL2**

**Objective**: Configure WSL2 to use Windows proxy settings.

- **Command** to enable Auto Proxy in `.wslconfig`:
  ```ini
  [wsl2]
  autoProxy=true
  ```

- **Why**: This ensures that any proxy configuration set in Windows is automatically mirrored within WSL2, allowing for consistent network access when behind a corporate firewall or when using proxy services.

---

### 7. **Kubeconfig and Context Management**

**Objective**: Ensure that Kubernetes contexts for Minikube and Docker Desktop are correctly set up and merged into the kubeconfig file.

- **Commands to manage Kubernetes contexts**:
  - Switch to a specific context:
    ```bash
    kubectl config use-context minikube
    ```
  - View all available contexts:
    ```bash
    kubectl config get-contexts
    ```

- **Reason**: We had to ensure the right context was selected and persisted in the kubeconfig file. This required merging contexts from Docker Desktop and Minikube into the config.

- **Issue**: We faced challenges with the kubeconfig file not properly containing all necessary contexts or configuration data. Using:
  ```bash
  kubectl config view --raw > ~/.kube/config
  ```
  helped export the correct settings.

---

### 8. **Final Validation Steps**

**Objective**: Validate all changes and ensure all services and contexts were correctly set up.

- **Testing Commands**:
  - Verify connectivity and service exposure in Minikube:
    ```bash
    kubectl get services
    minikube service list
    ```

- **Why**: To ensure the Kubernetes clusters (Docker Desktop and Minikube) are reachable and functioning as expected from within WSL2.

---

### 9. **Clearing and Rebuilding Configuration**

**Objective**: Fix kubeconfig issues by clearing and rebuilding configuration files.

- **Command** to clear any conflicting or broken configurations:
  ```bash
  kubectl config unset current-context
  kubectl config use-context docker-desktop
  ```

- **Why**: This was necessary to reset the kubeconfig file and prevent conflicts between the different Kubernetes contexts, ensuring Minikube and Docker Desktop could both be properly managed.

---

### 10. **Final Network Testing**

**Objective**: Ensure that all network configurations (e.g., Hyper-V, Docker, WSL2) were working as expected, and that Kubernetes services were exposed and accessible.

- **Commands for testing connectivity**:
  ```bash
  curl http://127.0.0.1:<port>
  ```
  - Ensured that services running in Minikube were reachable from the host machine via the browser or curl commands.

---

### **Summary of Changes**:
- **Hyper-V firewall** settings were adjusted to allow inbound traffic for WSL2 services.
- **Docker Desktop Kubernetes** context was correctly set up to allow interaction with Kubernetes from WSL2.
- **Minikube Kubernetes** cluster was initialized and tested, with fixes made for network-related issues.
- **DNS tunneling** was enabled to ensure proper DNS resolution within WSL2, improving compatibility with networks and VPNs.
- **Auto Proxy** settings were configured to automatically use Windows' proxy settings in WSL2.
- **Kubeconfig** was managed and merged to handle multiple Kubernetes contexts (Minikube and Docker Desktop).

---

That’s everything we’ve covered in the last 10 hours! Let me know if any part of it needs further explanation or if you'd like to move on to the next steps.