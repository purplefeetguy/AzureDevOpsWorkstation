# Azure DevOps Engineer Workstation Setup

This repository contains setup and configuration instructions for preparing a Windows 11 development workstation optimized for Azure DevOps engineering tasks. It focuses on infrastructure-as-code (IaC), automation, and cloud-native tools using WSL2 with Ubuntu.

## 📁 Repository Structure

```
.
├── docs/
│   └── AzureDevOpsEngineerWorkstationSetup.md
├── changelog/
│   └── CHANGELOG.md
├── .github/
│   └── workflows/
│       └── changelog-versioning.yml
└── README.md
```

## 🚀 What's Included

- Step-by-step guide for setting up:
  - WSL2 and Ubuntu on Windows 11
  - Docker Desktop
  - Azure CLI, Terraform, Bicep
  - VS Code with recommended extensions
- Final validation workflow for end-to-end environment testing
- Kubernetes networking tweaks (NodePort, Minikube, etc.)
- Versioned changelog and GitHub Actions automation

## 🧰 Requirements

- Windows 11 with admin rights
- WSL2 support
- Internet access to download packages

## 🔧 Usage

1. Clone this repository:

```bash
git clone https://github.com/<your-org>/azure-devops-workstation-setup.git
cd azure-devops-workstation-setup
```

2. Follow the instructions in `docs/AzureDevOpsEngineerWorkstationSetup.md` to configure your workstation.

3. Run the final validation section to confirm a working setup:

```bash
az login
terraform init
terraform apply
```

## 🔄 Versioning

Versions are tracked in `changelog/CHANGELOG.md` and updated dynamically by a GitHub Actions workflow.

- Major, minor, and patch bumps are inferred from commit messages
- The version is always listed on the first line of the changelog

## 🧪 GitHub Actions CI

A workflow is included to bump versions and commit changelog changes automatically.

To trigger it:
- Use commit messages with `feat`, `fix`, or `BREAKING CHANGE`
- Push to any branch

---

Your one-stop DevOps workstation bootstrap is just a few commands away. 🛠️⚙️☁️

Questions or feedback? Open an issue or start a discussion!

