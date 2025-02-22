# Server Setup Guide

A comprehensive guide for setting up new DevLab servers.

## Hardware Requirements

### Recommended Specifications

- **Server Model**: Dell Wyse 5070 thin client (or newer)
- **Storage**: Minimum 128GB
- **RAM**: Minimum 4GB
- **CPU**: Minimum 4 cores

> **Note**: We primarily use Dell Wyse thin clients due to their cost-effectiveness and x86 architecture. These can be found used on eBay, typically having served in office environments with minimal wear.

### Setup Requirements

- USB drive (8GB+)
- Keyboard (required for Ubuntu Server interface)
- Monitor (Dell Wyse 5070 uses DisplayPort or VGA)
- Computer with USB port (for ISO flashing)
- Power cable (verify voltage/wattage/amperage if not included)

## Installation Process

### 1. Prepare Installation Media

1. Download the latest Ubuntu Server LTS from [ubuntu.com/download/server](https://ubuntu.com/download/server)
2. Flash the ISO to USB using:
   - Windows: Rufus (recommended)
   - MacOS/Linux: Balena Etcher
   > **Important**: Use GPT partition scheme for better compatibility

### 2. Server Installation

1. Boot server to BIOS menu (Dell Wyse 5070: Press F12)
2. Select USB drive and choose "Install Ubuntu Server"
3. Configure the following settings:

#### User Configuration

- Full Name: `maintenance`
- Hostname: `devlab-[next_number_in_sequence]`
- Username: `maintenance`
- Password: Generate a random password (14+ characters)

#### System Configuration

- Disable LVM for disk setup
- Enable SSH server
- Enable third-party proprietary drivers
- Skip optional software installation (including Docker)

### 3. Post-Installation Setup

#### Enable SSH

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

#### Update System

```bash
sudo apt update && sudo apt upgrade -y
```

#### Install Tailscale

1. Install package:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

2. Start Tailscale:

```bash
sudo tailscale up
```

3. Complete web authentication when prompted

4. Enable Tailscale on boot:

```bash
sudo systemctl enable tailscaled
```

5. Disable key expiry:
   - Go to the Tailscale admin dashboard
   - Locate the newly added machine
   - Click the three dots (⋮) menu next to the server
   - Disable "Key expiry"

#### Install Docker

Follow these steps if the [standard installation](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository) fails:

1. Install prerequisites:

```bash
sudo apt install curl apt-transport-https ca-certificates software-properties-common
```

2. Add Docker GPG key:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

3. Add Docker repository:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

4. Update and install Docker:

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

5. Verify installation:

```bash
sudo docker run hello-world
```

## Verification

- Confirm Tailscale connectivity by checking the dashboard
- Verify SSH access using the hostname
- Ensure Docker is running correctly with the hello-world test
