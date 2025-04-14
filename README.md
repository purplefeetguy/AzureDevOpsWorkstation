# 🖥️ Azure DevOps Engineer Workstation Setup

![GitHub last commit](https://img.shields.io/github/last-commit/purplefeetguy/AzureDevOpsWorkstation?style=flat-square)
![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/purplefeetguy/AzureDevOpsWorkstation/changelog.yml?style=flat-square)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
![Issues](https://img.shields.io/github/issues/purplefeetguy/AzureDevOpsWorkstation)


This repository contains setup and configuration instructions for preparing a Windows 11 development workstation optimized for Azure DevOps engineering tasks. It focuses on infrastructure-as-code (IaC), automation, and cloud-native tools using WSL2 with Ubuntu.


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

1. Download AzureDevOpsEngineerWorkstationSetup.md which can be found in the docs directory.
```bash
curl -o AzureDevOpsEngineerWorkstationSetup.md https://raw.githubusercontent.com/purplefeetguy/AzureDevOpsWorkstation/refs/heads/update-0.6.0/docs/AzureDevOpsEngineerWorkstationSetup.md
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

## 🤪 GitHub Actions CI

A workflow is included to bump versions and commit changelog changes automatically.

To trigger it:
- Use commit messages with `feat`, `fix`, or `BREAKING CHANGE`
- Push to any branch

---

Your one-stop DevOps workstation bootstrap is just a few commands away. 🛠️⚙️☁️

Questions or feedback? Open an issue or start a discussion


## 📁 Repository Directory Structure
```
.
├── LICENSE
├── README.md
├── archive/
│   └── Windows11azureWorkstation.md
├── changelog/
│   └── CHANGELOG.md
├── docs/
│   └── AzureDevOpsEngineerWorkstationSetup.md
├── legacy/
│   ├── Windows11AzureWorkstation.0.1.0.md
│   ├── Windows11AzureWorkstation.0.3.0.md
│   ├── Windows11AzureWorkstation.0.4.0.md
│   ├── Windows11AzureWorkstation.0.5.0.md
│   ├── Windows11AzureWorkstation.0.5.1.md
├── scratch/
│   ├── scratch.md
│   ├── scratch2.md
│   └── scratch3.md
├── scripts/
└── terraform/
```

This reflects the following structure:

- **LICENSE**: Contains the licensing information (MIT License).
- **README.md**: Overview of the repository and its contents.
- **archive**: Houses archived documentation from older versions.
- **changelog**: Holds the `CHANGELOG.md` with the version history.
- **docs**: Contains the most up-to-date Azure DevOps Engineer Workstation setup documentation.
- **legacy**: Stores historical versions of the documentation for reference.
- **scratch**: Temporary notes or drafts.
- **scripts**: Directory for scripts that support the setup process.
- **terraform**: Directory for Terraform configuration files.


## Workflow for Maintaining the Documentation

This repository is primarily used to update and manage the Azure DevOps workstation setup documentation. Here's a general flow for maintaining the documents:

1. **Update Documentation**: When changes are made to the Azure DevOps workstation setup process (e.g., new tools or configurations), the documentation is updated in the `docs/AzureDevOpsEngineerWorkstationSetup.md` file.

2. **Versioning**: All notable changes are tracked in the `changelog/CHANGELOG.md` file using [Semantic Versioning](https://semver.org/). Each commit that alters the setup or structure of the documentation is accompanied by an incremented version (major, minor, or patch).

3. **Archiving Old Versions**: When updates are made, older versions of the documentation are moved to the `legacy` directory. This keeps the `docs` directory focused on the latest setup details while preserving historical context.

4. **Changelog Automation**: The versioning and changelog update process is automated using a GitHub Actions workflow. The `Dynamic Changelog Versioning` action handles version bumping (major, minor, or patch) based on commit messages and updates the `CHANGELOG.md` automatically.

5. **Collaboration**: Engineers and collaborators can contribute to the documentation by creating pull requests, updating the docs as needed, and following the versioning rules.

## How to Contribute

1. **Optional - Clone the Repository**: Although this repository is mainly for managing documentation, you can clone it to make changes locally.
    ```bash
    git clone https://github.com/purplefeetguy/AzureDevOpsWorkstation.git
    ```

2. **Make Changes**: Update the documentation or scripts as necessary.

3. **Commit Changes**: Use clear and concise commit messages. Ensure your commit messages align with the versioning policy (e.g., `feat: Added new tool setup` for a minor version update).

4. **Create Pull Requests**: Once your changes are complete, submit a pull request for review. Ensure that any changes made to the documentation are reflected in the appropriate version.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

This project was developed with assistance from [ChatGPT](https://openai.com/chatgpt), an AI language model by OpenAI, which helped with the documentation, content generation, and development of setup instructions.
