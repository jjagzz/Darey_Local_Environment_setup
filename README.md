# Darey_Local_Environment_setup
# Local DevOps Development Environment Setup

## Overview

This project documents the setup of a production-grade local development workstation for DevOps work, built on **Windows 11 with WSL2 (Ubuntu)**. The goal was to establish a consistent, reproducible toolchain covering version control, containerization, Kubernetes orchestration, infrastructure as code, cloud CLIs, and AI-assisted development tooling — eliminating "works on my machine" inconsistencies.

## Environment

| Component | Detail |
|---|---|
| Host OS | Windows 11 |
| Linux Layer | WSL2 (Ubuntu) |
| Linux Package Manager | Apt |
| RAM | 4GB minimum (verified via `wsl --status` / Task Manager) |
| Disk Space | 10GB minimum free (verified via `df -h`) |

## Package Managers Used

This setup used a **hybrid package manager approach**, which is standard practice for WSL2-based DevOps environments:

- **Winget** (Windows Package Manager) — used to install Windows-side GUI applications and tools that benefit from native Windows integration: Visual Studio Code, Docker Desktop, and Git for Windows.
- **Apt** (Advanced Package Tool) — used inside the WSL2 Ubuntu distribution to install Linux-native CLI tools: `kubectl`, `helm`, `terraform`, `ansible`, `jq`, Node.js, and the AWS/Azure CLIs.

The split exists because Docker Desktop integrates with WSL2 as a backend (rather than running natively inside the Linux distro), while most CLI-based DevOps tools are more reliable and behave more consistently when installed directly inside the Linux environment rather than the Windows host.

## Tools Installed

### 1. Host Environment Preparation
- Enabled WSL2 via `wsl --install` and set Ubuntu as the default distribution (`wsl --set-default-version 2`).
- Verified Windows was updated via Windows Update, and Ubuntu packages were refreshed with `sudo apt update && sudo apt upgrade -y`.

### 2. Version Control & Editor
- Installed **Git** via `sudo apt install git -y` inside WSL2, and configured global identity:
  ```bash
  git config --global user.name "<name>"
  git config --global user.email "<email>"
  ```
- Connected Git to GitHub using SSH key authentication.
- Installed **Visual Studio Code** via `winget install Microsoft.VisualStudioCode` on the Windows side, then added the **WSL extension** so VS Code can open and edit files directly inside the Ubuntu filesystem.

VSCODE
![](./screenshots/VScode%20installation.jpg)

GIT
![](./screenshots/git%20install%20verification.jpg)

GITHUB
![](./screenshots/GIThub%20setup.jpg)

### 3. AI-Assisted Tooling (Optional/Advanced)
- Explored **GitHub Copilot** as a VS Code extension for AI-assisted code completion within the DevOps workflow.

### 4. Containerization
- Installed **Docker Desktop** via `winget install Docker.DockerDesktop` on Windows, enabling the **WSL2-based engine** and turning on WSL integration for the Ubuntu distro under Docker Desktop's settings (Settings → Resources → WSL Integration).
- Verified Docker Compose was bundled with Docker Desktop (`docker compose version`).

### 5. Kubernetes Tooling
- Installed **Minikube** inside WSL2 via direct binary download and `install` to `/usr/local/bin`.
- Installed **kubectl** via Apt (`sudo apt install kubectl -y` from the Kubernetes apt repository).
- Installed **Helm** via the official install script (`curl` + `bash get_helm.sh`).
- Started a local cluster with `minikube start --driver=docker`, using Docker Desktop's engine as the driver since WSL2 doesn't support a hypervisor-based driver directly.

### 6. Cloud CLIs & Language Runtimes
- Installed **AWS CLI v2** via the official Linux `curl` install script inside WSL2.
- Installed **Azure CLI** via the official Microsoft Apt repository script.
- Installed **Node.js** (LTS) via `nvm` (Node Version Manager) inside WSL2 for flexible version switching.
- Installed **jq** via `sudo apt install jq -y`.

AWSCLI 
![](./screenshots/aws%20verison.jpg)

### 7. Infrastructure & Configuration Management
- Installed **Terraform** via HashiCorp's official Apt repository.
- Installed **Ansible** via `sudo apt install ansible -y`.

### 8. Verification & Security
- Ran version checks on all tools (see `verification-report.txt`).
- Checked file/script permissions with `ls -l` and ensured SSH key permissions were locked down (`chmod 600 ~/.ssh/id_rsa`).

## Troubleshooting Steps Taken

1. **Docker not found inside WSL2** — Initially, `docker` commands weren't recognized inside Ubuntu even though Docker Desktop was installed and running on Windows. Fixed by enabling WSL Integration for the specific Ubuntu distro under Docker Desktop → Settings → Resources → WSL Integration, then restarting the WSL terminal.

2. **`kubectl` version mismatch with Minikube** — Ran into a client/server version skew warning. Resolved by reinstalling `kubectl` via the official Kubernetes apt repository (rather than a snap package) to ensure compatibility with the Minikube-managed API server version.

3. **Minikube failing to start with default driver** — Minikube attempted to use a `none` or `virtualbox` driver by default, which isn't supported inside WSL2. Fixed by explicitly specifying `minikube start --driver=docker` to use the Docker Desktop backend already running.

4. **PATH inconsistencies between Windows and WSL2** — Tools installed via Winget on Windows weren't visible inside the WSL2 shell (and vice versa), since WSL2 and Windows maintain separate PATH environments (WSL2 appends Windows PATH by default, but not the reverse). Resolved by installing CLI tools natively inside WSL2 via Apt instead of relying on the Windows-side installs, keeping the Linux toolchain self-contained.

5. **SSH key permission errors with Git** — Git initially rejected the SSH key with a "permissions too open" error, because the WSL2 filesystem mounted from Windows doesn't preserve strict Unix permissions correctly. Fixed by generating and storing the SSH key inside the native WSL2 Linux filesystem (`~/.ssh`) rather than a Windows-mounted path (`/mnt/c/...`), then applying `chmod 600`.

## Submission Contents

1. `README.md` — this file, documenting the setup process and troubleshooting.
2. `verification-report.txt` — version check output for all installed tools.
3. Screenshot of `minikube start` and `kubectl get nodes` showing a running local cluster.

