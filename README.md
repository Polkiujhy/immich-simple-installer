# Immich Simple Installer

A WSL2-focused bash script for easy installation and configuration of [Immich](https://immich.app) on Intel CPU + NVIDIA GPU systems, including automatic hardware acceleration detection and setup

## 🚀 Features

### Core Installation
- **Automated Download**: Downloads latest `docker-compose.yml` and `.env` files from Immich releases
- **Interactive Configuration**: Guided setup for all environment variables
- **Docker Validation**: Checks Docker and Docker Compose installation and status
- **WSL Focus**: Purpose-built for Windows Subsystem for Linux 2 on Ubuntu

### Hardware Acceleration
- **Video Transcoding**: Automatic detection and setup for `nvenc`
- **Machine Learning**: Support for `cuda`
- **Smart Detection**: Identifies available hardware and suggests optimal configurations
- **Disable Options**: Easy removal of existing hardware acceleration configurations

### Environment Setup
- **Password Generation**: Automatic secure database password generation
- **Timezone Configuration**: Interactive timezone selection with reference links
- **Volume Management**: WSL-specific database volume handling for compatibility
- **Version Control**: Option to pin specific Immich versions

## �️ Supported Platforms

This script targets:
- **Windows Subsystem for Linux (WSL2)**
- **Ubuntu on WSL2**
- **Intel CPU hosts**
- **NVIDIA GPU acceleration**

Non-WSL Linux paths and non-NVIDIA acceleration routes were intentionally removed to keep the installer aligned with the target hardware.

## �📋 Prerequisites

- **Operating System**: Windows with WSL2 Ubuntu
- **Docker**: [Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/)
- **Network Access**: Internet connection for downloading files
- **Permissions**: Ability to create directories and modify files

### Hardware-Specific Requirements

#### NVIDIA (NVENC/CUDA)
- NVIDIA GPU with driver installed
- NVIDIA support exposed into WSL2
- For ML: GPU with compute capability 5.2+ and driver ≥545

#### Intel CPU
- CPU vendor must be reported in WSL as `GenuineIntel`
- No Intel GPU acceleration path is configured by this installer

## 🔧 Installation

### Full Setup From Scratch

This is the recommended first-time setup flow for a new machine:

1. Install WSL2 and Ubuntu in Windows.
2. Install Docker Desktop on Windows and turn on WSL integration for Ubuntu.
3. Update WSL and confirm GPU access from Ubuntu.
4. Install a few Ubuntu utilities and clone this repository.
5. Run the installer script from inside Ubuntu.

### 1. Install WSL2 Ubuntu

If WSL2 Ubuntu is not installed yet, open **PowerShell as Administrator** in Windows and run:

```powershell
wsl --install -d Ubuntu
```

Then:

1. Restart Windows if prompted.
2. Launch `Ubuntu` from the Start menu.
3. Create your Linux username and password when Ubuntu starts the first time.
4. Confirm WSL2 is active:

```powershell
wsl --list --verbose
```

`Ubuntu` should show version `2`. If WSL is already installed but Ubuntu is missing, you can still use `wsl --install -d Ubuntu`.

Reference: [Install WSL | Microsoft Learn](https://learn.microsoft.com/en-us/windows/wsl/install)

### 2. Update WSL

Still in **PowerShell**, update WSL to the latest version and kernel:

```powershell
wsl --update
```

You can check the installed WSL version with:

```powershell
wsl --version
```

### 3. Install Docker Desktop on Windows

Install Docker Desktop on Windows, not Docker Engine directly inside Ubuntu.

1. Download and install Docker Desktop for Windows.
2. During setup, keep the `Use WSL 2 instead of Hyper-V` option enabled.
3. Start Docker Desktop once the install finishes.
4. Accept the Docker terms if prompted.

Reference: [Install Docker Desktop on Windows | Docker Docs](https://docs.docker.com/desktop/setup/install/windows-install/)

### 4. Enable Docker for Ubuntu in WSL

In Docker Desktop on Windows:

1. Open `Settings`.
2. In `General`, make sure `Use WSL 2 based engine` is enabled.
3. Go to `Settings > Resources > WSL Integration`.
4. Enable integration for your `Ubuntu` distribution.
5. Click `Apply`.

Docker recommends using Docker Desktop's WSL integration instead of installing Docker Engine separately inside the WSL distro.

Reference: [Docker Desktop WSL 2 backend on Windows | Docker Docs](https://docs.docker.com/desktop/features/wsl/)

### 5. Make Sure NVIDIA GPU Support Works

For the accelerated path this installer targets, Windows must expose the NVIDIA GPU into WSL2.

Before continuing:

1. Install or update the current Windows NVIDIA driver with WSL2 GPU support.
2. Keep Docker Desktop configured to use the WSL2 backend.
3. Make sure `wsl --update` has been run recently.

Reference: [GPU support in Docker Desktop for Windows | Docker Docs](https://docs.docker.com/desktop/features/gpu/)

### 6. Open Ubuntu and Install Basic Tools

From this point on, run commands inside **Ubuntu**, not in PowerShell.

Update packages and install the basic tools used by this README and installer:

```bash
sudo apt update
sudo apt install -y git curl wget
```

### 7. Verify Docker and GPU Access Inside Ubuntu

Inside Ubuntu, check:

```bash
docker --version
docker compose version
docker info
nvidia-smi
grep -m1 '^vendor_id' /proc/cpuinfo
```

Expected results:

- `docker info` should work without daemon errors.
- `nvidia-smi` should show your NVIDIA GPU from inside Ubuntu.
- `vendor_id` should report `GenuineIntel`.

Optional Docker-level GPU validation from Docker's docs:

```bash
docker run --rm -it --gpus=all nvcr.io/nvidia/k8s/cuda-sample:nbody nbody -gpu -benchmark
```

### 8. Clone This Repository

Inside Ubuntu:

```bash
git clone https://github.com/Polkiujhy/immich-simple-installer.git
cd immich-simple-installer
```

### 9. Run the Installer

Inside Ubuntu:

```bash
chmod +x immich_installer.sh
./immich_installer.sh
```

### Quick Start

If WSL2 Ubuntu, Docker Desktop WSL integration, and NVIDIA access are already working, you can just run:

```bash
git clone https://github.com/Polkiujhy/immich-simple-installer.git
cd immich-simple-installer

# Make it executable
chmod +x immich_installer.sh

# Run the installer
./immich_installer.sh
```

### Custom Installation Directory

```bash
# Specify installation folder
./immich_installer.sh -f /path/to/immich
```

### Help and Usage

```bash
# Show help message
./immich_installer.sh -h
```

## 🎯 Usage

### Interactive Installation

The script will guide you through:

1. **Docker Verification**: Checks Docker installation and status
2. **Directory Setup**: Creates installation folder if needed
3. **File Download**: Downloads latest Immich files
4. **Environment Configuration**:
   - Upload location (default: `./library`)
   - Database location (default: `./postgres` or Docker volume for WSL)
   - Timezone selection
   - Immich version selection
   - Database password (auto-generated or custom)

5. **Hardware Transcoding Setup**:
   - Automatic API detection (`nvenc`)
   - Interactive selection from available options
   - Manual configuration option
   - Disable existing configurations

6. **ML Hardware Acceleration**:
   - Backend detection (`cuda`)
   - Performance optimization suggestions
   - Environment variable recommendations

7. **Container Startup**: Option to start Immich immediately

### Configuration Options

#### Hardware Transcoding Responses
- **Y/Enter**: Configure detected hardware acceleration
- **n**: Skip hardware transcoding setup
- **disable**: Remove existing hardware acceleration

#### ML Acceleration Responses
- **Y/Enter**: Configure detected ML acceleration
- **n**: Skip ML hardware acceleration
- **disable**: Remove existing ML acceleration

#### Manual Configuration
When auto-detection fails, you can manually specify:
- **Transcoding**: `nvenc`
- **ML**: `cuda`

## 📁 Generated Files

After successful installation:

```
your-installation-folder/
├── docker-compose.yml          # Main compose file
├── .env                        # Environment variables
├── hwaccel.transcoding.yml     # Hardware transcoding config (if enabled)
├── hwaccel.ml.yml             # ML acceleration config (if enabled)
└── volumes/
    ├── library/               # Photo/video storage
    └── postgres/              # Database files (or Docker volume)
```

## 🔍 Hardware Detection

### Video Transcoding APIs

| API | Detection Method | Requirements |
|-----|------------------|--------------|
| **NVENC** | `nvidia-smi` in WSL | NVIDIA GPU exposed to WSL2 |

### ML Acceleration Backends

| Backend | Detection Method | Requirements |
|---------|------------------|--------------|
| **CUDA** | NVIDIA GPU + Compute 5.2+ | NVIDIA GPU exposed to WSL2 |

## ⚙️ Advanced Configuration

### Environment Variables

The script configures these key variables in `.env`:

```bash
# Storage locations
UPLOAD_LOCATION=./library
DB_DATA_LOCATION=./postgres  # or 'pgdata' for WSL Docker volume

# Database configuration
DB_PASSWORD=<generated-secure-password>

# Version control
IMMICH_VERSION=release  # or specific version like 'v1.71.0'

# Timezone
TZ=America/New_York  # or your timezone
```

### Performance Optimization

#### ML Acceleration Environment Variables

Add these to `.env` for optimal performance:

```bash
# CUDA Multi-GPU
MACHINE_LEARNING_DEVICE_IDS=0,1
MACHINE_LEARNING_WORKERS=2
```

### WSL-Specific Considerations

For WSL2 Ubuntu users:

- **Database Volume**: Script automatically suggests Docker volumes over bind mounts
- **Filesystem Compatibility**: Unsafe database bind mounts on `/mnt` and other Windows-backed filesystems are rejected
- **Scope**: Non-WSL install paths and non-NVIDIA acceleration backends were removed
- **CPU Target**: Installer exits on non-Intel CPU vendors

## 🐛 Troubleshooting

### Common Issues

#### Docker Problems
```bash
# Check Docker status
docker --version
docker compose version
docker info

# If Docker is unavailable in WSL:
# 1. Start Docker Desktop on Windows
# 2. Confirm WSL integration is enabled for Ubuntu
```

#### Hardware Not Detected
```bash
# Check NVIDIA
nvidia-smi
docker run --rm -it --gpus=all nvcr.io/nvidia/k8s/cuda-sample:nbody nbody -gpu -benchmark

# Confirm the CPU vendor reported inside WSL
grep -m1 '^vendor_id' /proc/cpuinfo
```

#### Permission Issues
```bash
# Add user to docker group
sudo usermod -aG docker $USER
# Log out and back in
```

### Hardware-Specific Troubleshooting

#### NVIDIA CUDA
- Verify driver version: `nvidia-smi`
- Check compute capability: GPU must be 5.2+
- Make sure GPU support is exposed into WSL2 by your Windows driver and Docker setup

#### Intel CPU Targeting
- Confirm WSL reports `vendor_id : GenuineIntel`
- If this check fails on your machine, this installer will now exit early

## 📖 References

- [Immich Documentation](https://immich.app/docs/)
- [Hardware Transcoding Guide](https://immich.app/docs/features/hardware-transcoding)
- [ML Hardware Acceleration](https://immich.app/docs/features/ml-hardware-acceleration)
- [Docker Compose Installation](https://docs.docker.com/compose/install/)

## 📝 License

This installer script is licensed under the [MIT License](https://opensource.org/licenses/MIT).

```
MIT License

Copyright (c) 2025 Immich Simple Installer

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

**Note**: This installer script is licensed under MIT. Immich itself is licensed under [GNU AGPL v3](https://github.com/immich-app/immich/blob/main/LICENSE).

## 🤝 Contributing

Feel free to submit issues, suggestions, or improvements to enhance the installer script.

---

**Note**: This is an unofficial installer script. For official installation methods, please refer to the [Immich documentation](https://immich.app/docs/install/).
