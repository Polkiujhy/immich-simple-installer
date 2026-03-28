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
- **Docker**: [Docker Engine and Docker Compose v2](https://fixtse.com/blog/open-webui#docker-installation-linuxwsl)
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

### Install WSL2 Ubuntu

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

### Quick Start

```bash
# Download the script
wget -O immich_installer.sh https://raw.githubusercontent.com/fixtse/immich-simple-installer/main/immich_installer.sh
# or
curl -fsSL -o immich_installer.sh https://raw.githubusercontent.com/fixtse/immich-simple-installer/main/immich_installer.sh

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

# If Docker is unavailable in WSL, start Docker Desktop on Windows first
```

#### Hardware Not Detected
```bash
# Check NVIDIA
nvidia-smi
docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi

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
