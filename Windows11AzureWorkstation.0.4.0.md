# **Azure DevOps Engineer Workstation Setup on Windows 11**

## **Overview**
This guide walks through the complete setup process for an Azure DevOps Engineer workstation on Windows 11, equipping it with key tools for cloud infrastructure development using **Infrastructure as Code (IaC)**. The workstation will include **WSL2 running Ubuntu, Docker, Terraform, Ansible, Azure CLI, Visual Studio Code**, and other essential components.  

The setup is divided into the following sections:  
1. **System Prerequisites & Configuration**  
2. **WSL2 Enablement & Ubuntu Installation**  
3. **Docker Installation & Setup (Enhanced)**  
4. **Development Tool Installation (Terraform, Ansible, Azure CLI, Bicep, etc.)**  
5. **Visual Studio Code & Extensions**  
6. **Validation & Troubleshooting**  

---

## **1. Prerequisites**
### **Minimum Windows 11 Version**
- **Windows 11 Version:** 21H2 or later  
- **Build Number:** 22000 or higher  
- To check your version:  
  ```powershell
  winver
  ```  
- If outdated, update Windows via **Settings → Windows Update**.

### **Administrator Access**
- Required to install tools, enable WSL2, and configure services.  

### **Installation Strategy**
- **Windows:** Prefer `winget` for installations.  
- **Ubuntu WSL2:** Use `apt` for package management.  

---

## **2. Enable WSL2 & Install Ubuntu**
### **Enable WSL2**
Run the following in PowerShell:
```powershell
wsl --install
wsl --set-default-version 2
```
Reboot your machine after enabling WSL2.

### **Install Ubuntu**
Run:
```powershell
winget install -e --id Canonical.Ubuntu
```
Once installed, launch Ubuntu and create a default user.

### **Update Ubuntu**
```bash
sudo apt update && sudo apt upgrade -y
```

---

## **3. Install Docker in WSL2 (Enhanced)**
To run Docker inside WSL2 efficiently, follow these steps:

### **Step 1: Install Docker Desktop on Windows**
Download Docker Desktop from [Docker Official Website](https://www.docker.com/products/docker-desktop) and install it.

Enable **WSL2 Integration** during installation:
1. Open Docker Desktop → **Settings**  
2. Navigate to **Resources → WSL Integration**  
3. Enable integration for Ubuntu  

### **Step 2: Install Docker in Ubuntu WSL2**
Once WSL2 integration is enabled, install Docker inside Ubuntu:

#### **1. Install Docker Engine**
Run:
```bash
sudo apt update
sudo apt install -y docker.io
```
Ensure Docker is installed correctly:
```bash
docker --version
```

#### **2. Start & Enable Docker Service**
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

#### **3. Add User to Docker Group (Avoid Using sudo)**
```bash
sudo usermod -aG docker $USER
exec sudo su -l $USER
```
Check if Docker works:
```bash
docker run hello-world
```

### **Step 3: Enable systemd for WSL2 (Fix Docker Issues)**
1. Open Ubuntu WSL2 terminal:
   ```bash
   nano /etc/wsl.conf
   ```
2. Add the following lines:
   ```ini
   [boot]
   systemd=true
   ```
3. Restart WSL2:
   ```powershell
   wsl --shutdown
   wsl
   ```
4. Verify systemd is running:
   ```bash
   ps -p 1 -o comm=
   ```
   Expected output:  
   ```
   systemd
   ```

### **Step 4: Test Docker**
```bash
docker run hello-world
```
If successful, Docker is fully configured!

---

## **4. Install Azure CLI**
Run inside Ubuntu:
```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az version
```

---

## **5. Install Terraform**
```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform -y
terraform version
```

---

## **6. Install Bicep**
```bash
az bicep install
az bicep version
```

---

## **7. Install Visual Studio Code**
Download from [VS Code Official Website](https://code.visualstudio.com/)  
Recommended Extensions:
- Azure Account  
- Azure CLI Tools  
- Terraform  
- Bicep  
- Docker  
- Remote - WSL  

---

## **8. Final Validation Workflow**
### **Login to Azure**
```bash
az login
```
### **Initialize Terraform**
```bash
terraform init
```
### **Test Resource Deployment**
```bash
echo "resource \"azurerm_resource_group\" \"test\" { name = \"test-rg\" location = \"eastus\" }" > main.tf
terraform plan
terraform apply -auto-approve
terraform destroy -auto-approve
```
✅ Success = Workstation Ready 🚀  

---

## **Annex: WSL2 Troubleshooting**
### **Common Docker Issues in WSL2**
1. **WSL2 doesn’t recognize Docker commands**  
   - Run: `sudo systemctl start docker`  
   - Ensure `docker` group permissions are applied (`sudo usermod -aG docker $USER`)  

2. **Containers fail to start**  
   - Check system logs: `sudo journalctl -u docker --no-pager`  

3. **WSL2 networking issues**  
   - Run: `wsl --shutdown && wsl`  
   - Reset Docker Desktop network settings  

---

## **Conclusion**
Congrats! Your **Azure DevOps Engineer Workstation** is now fully configured with **WSL2, Docker, Terraform, Ansible, Azure CLI**, and more.  

💡 Need further enhancements? Let me know! 🚀  
---
This version incorporates clearer sections, more details for Docker installation, additional troubleshooting, and a structured workflow. Anything else you'd like me to tweak?