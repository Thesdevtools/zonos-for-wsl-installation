# Unofficial Zonos WSL Installation Guide

> [!WARNING]
> This is an **unofficial** guide maintained by the community.  
> While we strive for accuracy, Zonos is currently in active development (Beta status), and instructions may change.  
> For the official documentation, please visit the [Official Zonos Repository](https://github.com/Zyphra/Zonos).

> [!NOTE]
> **Japanese Version (日本語版)**: [README_ja.md](README_ja.md)

This repository provides clear, step-by-step instructions for installing **Zonos** on **WSL2 (Windows Subsystem for Linux)**.

---

## 📋 Prerequisites

- **OS**: Windows 10 or 11 with WSL2 enabled
- **Linux Distro**: Ubuntu, Debian, etc. (installed via WSL)
- **GPU**: NVIDIA GPU (6GB+ VRAM recommended)
  - **Hybrid Model**: Requires RTX 3000 series or newer
  - **Drivers**: Latest NVIDIA Drivers installed on Windows

---

## 🔧 Differences from Official Instructions

We have adapted the instructions specifically for the WSL environment:

| Item | Official Guide | Adaptation | Reason |
|------|----------------|------------|--------|
| **uv Installation** | `pip install -U uv` | Use official script | `pip` installation fails on some distributions (like Debian) due to PEP 668. |
| **Env Variables** | None | `source ~/.local/bin/env` | Required to set PATH for the script-installed `uv`. |
| **CUDA Toolkit** | Not mentioned | `nvidia-cuda-toolkit` | Required to build packages like `mamba-ssm` for the Hybrid model. |

---

## 🚀 Installation Steps

### Step 0: Start WSL

Open your Windows Terminal (PowerShell or Command Prompt) and run:

```powershell
wsl
```

**⚠️ Note: All subsequent commands must be run inside the WSL (Linux) shell.**

---

### Step 1: Install System Packages

Install necessary dependencies including `espeak-ng`, `git`, and `curl`.

```bash
sudo apt update
sudo apt install -y espeak-ng git curl
```

---

### Step 2: Clone Repository

```bash
cd ~
git clone https://github.com/Zyphra/Zonos.git
cd Zonos
```

---

### Step 3: Install uv

We use the official installer script to avoid PEP 668 issues.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.local/bin/env
```

---

### Step 4: Install Python Dependencies

```bash
uv sync
uv pip install -e .
```

---

### Step 5: Install Packages for Hybrid Model (Optional)

**Run this ONLY if you intend to use the Hybrid model.**  
(Requires NVIDIA RTX 3000 series or newer)

**For Debian Users**:
The `nvidia-cuda-toolkit` is in the `non-free` repository. You must enable it first.

```bash
# Add non-free repository (Only for Debian)
sudo sed -i 's/ main$/ main contrib non-free non-free-firmware/g' /etc/apt/sources.list
sudo apt update

# Install CUDA Toolkit
sudo apt install -y nvidia-cuda-toolkit

# Build packages
uv sync --extra compile
```

---

### Step 6: Verification

Run the sample script to verify installation.

```bash
uv run sample.py
```

If `sample.wav` is generated successfully, the installation is complete.

---

### Step 7: Start Gradio WebUI

```bash
uv run gradio_interface.py
```

Open `http://localhost:7860` in your browser.

---

## 📝 How to Launch Next Time

```bash
wsl
cd ~/Zonos
source ~/.local/bin/env
uv run gradio_interface.py
```

---

## 💡 Troubleshooting

- **`pip install -U uv` fails**:
  - Use the official script method in Step 3.
- **Build error with `uv sync --extra compile`**:
  - Ensure `nvidia-cuda-toolkit` is installed correctly.
- **GPU not recognized**:
  - Update your NVIDIA drivers on Windows.
  - Run `nvidia-smi` in WSL to verify visibility.

---

## 📚 References

- [Official Zonos Repository](https://github.com/Zyphra/Zonos)
- [uv Documentation](https://docs.astral.sh/uv/)
