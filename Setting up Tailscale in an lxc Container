# Setting up Tailscale in an lxc Container

Writen: 6/19/2025

Installing Tailscale onto a Proxmox host or in lxc is straightforward. Installing in an lxc will give you access to the container though ssh. From there, you can ssh into the node. To gain access to the node via Web UI set up ssh local port fowarding, a reverse ssh tunnel, or something similar.

[Create the Container](#Create-the-Container)

[In the Node](#In-the-Node)

[In the Container](#In-the-Container)

[Access the Container Through Tailscale](#Access-the-Container-Remotely)

[Local Port Fowarding for Web UI Access](#Local-Port-Fowarding)

[Check Tailscale Status](#Check-Tailscale-Status)

[Troubleshooting](#Troubleshooting)

[Links](#Links)


## Requirements:
- Tailscale Account
- Proxmox Host with CT

# Create the Container
Create a container using the “Create CT” in the top left hand conner. For the [container template](https://pve.proxmox.com/wiki/Linux_Container) chose debian-12. Name the container “tailscale” or something similar.

The container needs to be granted access to the correct network resources in order for Tailscale to work. This requires editing the LXC config files on the node.

# In the Node
In the node shell, open the containter config file:

```
nano /etc/pve/lxc/<lxc number>.conf
```

At the bottom of the .conf file (do not delete any lines) add the following lines:

```
lxc.cgroup2.devices.allow: c 10:200 rwm
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
```

Save and close the file.

# In the Container
Select the Tailscale container and launch its shell.

First, update the container:

```
sudo apt-get update
```

Next, install Tailscale’s signing key, repository, and Tailscale:

```
curl -fsSL https://pkgs.tailscale.com/stable/debian/bookworm.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null
curl -fsSL https://pkgs.tailscale.com/stable/debian/bookworm.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list
apt-get install tailscale
```

Run Tailscale:

```
sudo tailscale up
```

Copy and paste the provided link into your browser to add the container to your account.

# Access the Container Remotely
Start a terminal on your system.

Run:

```
ssh root@\<tailscale ip\>
```

Enter your container password 

To access the node from here, run:

```
ssh root@\<local proxmox ip\>
```

# Local Port Fowarding
To access the Web UI of the Tailscale device you have to setup ssh local port fowarding.

From your remote computer (the one that you are on) start a ssh connection to the device running Tailscale and enter your password:

```
ssh -L 8006:localhost:8006 <username>@<tailscaleip>
```
  - '-L' Specifies the username and IP to log into
  - '8006:localhost:8006' tells the local host to foward all traffic on port 8006 to itself
  - \<username\>@\<tailscaleip\> is the username and ip of the tailscale device

To connect to the Web UI, in your browser go to:

```
http://localhost:8006
```

# Check Tailscale Status
To check the Tailscale status and connections run:

```
tailscale status
```

This will bring up a list of Tailscale IPs and their connection status.

# Links 
Linux Container: https://pve.proxmox.com/wiki/Linux_Container

Install Tailscale in a Debain container: https://tailscale.com/kb/1174/install-debian-bookworm

Fix Tailscale ‘failed to connect to local tailscaled’ Error on Proxmox Containers: https://selfhostingsanctuary.com/servers-hardware/virtualization-containers/tailscale-failed-to-connect-to-local-tailscaled-error-on-proxmox-containers/

Tailscale in lxc containers: https://tailscale.com/kb/1130/lxc-unprivileged

# Troubleshooting
### Bash: curl: command not found
- Update the container using: sudo apt-get update

---

### "failed to connect to local tailscaled; it doesn't appear to be running (sudo systemctl start tailscaled ?)"
- Run the command: sudo systemctl start tailscaled

---

### "failed to connect to local tailscaled (which appears to be running as tailscaled, pid 1430). Got error: Failed to connect to local Tailscale daemon for /localapi/v0/status; systemd tailscaled.service not running. Error: dial unix /var/run/tailscale/tailscaled.sock: connect: no such file or directory"
- Go to the above Node section and run/check that the files are correct.
