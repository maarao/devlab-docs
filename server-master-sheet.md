Server Master Sheet
This is the guide for setting up a new server for the club

What should you buy:
As of now, we've only experiemented with Dell Wyse thin clients. You can usually snag a few used ones for cheap on ebay which we don't really care much about since they spent their lives as thin-clients in office spaces, so no real heavy use has been put on them. We specifically chose the Dell Wyse 5070 for our first machine, so I would recommend at least that or a newer spec'd one. Beware of if it comes with a power cable / storage or not. If it doesn't look up the spec sheet and it will usually tell you want voltage / wattage / amperage it needs.
As for specs outside of that:
storage, since it might be used a decent amount we recommend to get something that can support at least 128GB.
memory / ram, we recommend at least 4GB.
cpu: at minimum 4 cores


Setup process:
Prerequisites:
- A usb drive with at least 8GB of storage
- The server itself (see above to figure out what you want)
- A computer with a usb port to download and flash the iso to the usb drive
- A keyboard (ubuntu server is a keyboard-only interface)
- A monitor (beware of what connectors it has, for the dell wyse 5070 it is displayport or vga)

Install the latest lts version of ubuntu server at https://ubuntu.com/download/server in a directory you can easily access

flash the downloaded iso to a usb drive using either the perferred method of rufus (windows) or balena etcher (macos / linux) by selecting the downloaded iso and the usb drive. Try to ensure it is flashed as a GPT partition not MBR because it might be required for the server to boot with the usb drive.

Plug the usb into the server and boot it up. You are going to have to get it to the boot menu. You will have to look up the specific instructions for your specific machine. For the Dell Wyse 5070, it is F12.

Select you drive and select the option to install ubuntu server.

It will then walk you through a normal setup. To toggle between the options, you can use the arrow keys to navigate and ENTER to select. The only things you will have to input/change are the user configuration, the hostname, the hard drive configuration, enabling ssh.

for the user/hostname, we recommend these values to match the naming convention of the other servers:
Full Name: maintenance
Hostname: devlab-[next_number_available_in_the_sequence]
Username: maintenance
Password: randomly generated password minimum 12 characters

for the disks turn off the option to setup lvm.

there will be a section just for enabling ssh. please enable it here.

Notes:
It will show you some softawre including docker, but don't install anything here. We will handle that later.

It will also ask if you want to install thrid-party proprietary drivers. I recommend enabling that setting.

Once it is done, you will be asked to remove the usb drive and restart the server. Do so.

Once you are back at the login screen, you can login with the username and password you set up earlier.

Once you are logged in, you can run the following commands to configure the server:

Enable ssh:
```
sudo systemctl enable ssh
sudo systemctl start ssh
```

Update the server:
```
sudo apt update && sudo apt upgrade -y
```

Install Tailscale:
Tailscale is a tool that will allow you to ssh into the server from anywhere. You must have access to the devlab tailscale accounts to do this.
Install the tailscale package:
```
curl -fsSL https://tailscale.com/install.sh | sh
```

Start Tailscale:
```
sudo tailscale up
```
It will give you a tailscale login url. Open it in your browser and login with the tailscale account.

it should be on the tailscale network now. If you install and connect to the tailscale network on another device, you should be able to ssh into the server from that device using just the name of the device (hostname) you set before. You should also see this device going online in the tailscale dashboard.

Once you have verified that it is working, you must run the following command to start the tailscale service on boot:
```
sudo systemctl enable tailscaled
```

Install Docker:
Docker is a tool that allows you to run containers on your server.

This one is a little complex in that the traditional way might not work because we're on ubuntu server.
This official instructions are here under the "Install using the repository" section: https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

If it says it can't find packages, you might have to run these commands instead:
```
sudo apt install curl apt-transport-https ca-certificates software-properties-common
```

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```
sudo apt update
```

```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```


Regardless of which way you do it, run the following command to make sure it is installed correctly:
```
sudo docker run hello-world
```

That should be it for the initial setup.
