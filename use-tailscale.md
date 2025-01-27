How to use tailscale.

All devlab servers exist on a tailscale network that allows them to communicate with each other.

To connect to the tailnet, you need to install the tailscale client on your machine.

You can download the client from the [tailscale website](https://tailscale.com/download).

Once you have installed the client, you can launch it and login with your tailscale account. For us, that means clicking the "Log in with Google" button.

Once you are logged in, you should be able to connect to the tailnet. You can then ssh into any of the devlab servers using the following command:

```
ssh maintenance@<server-name>
```

Replace `<server-name>` with the name of the server you want to connect to. If you want to figure out the name of the server you want to connect to, you can go to the [tailscale dashboard](https://login.tailscale.com/admin/machines) and find the name of the machine you want to connect to.
