# Using Tailscale

## Overview
All devlab servers exist on a Tailscale network that allows them to communicate with each other.

## Installation
1. Install the Tailscale client on your machine
   - Download from the [Tailscale website](https://tailscale.com/download)

## Setup
1. Launch the Tailscale client
2. Click "Log in with Google" to authenticate with your account
3. Wait for connection to the tailnet to be established

## Connecting to Servers
You can SSH into any devlab server using:
```bash
ssh maintenance@<server-name>
```

To find the correct server name:
1. Visit the [Tailscale dashboard](https://login.tailscale.com/admin/machines)
2. Locate your target machine in the list
3. Use the displayed machine name in place of `<server-name>`
