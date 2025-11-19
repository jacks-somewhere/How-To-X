# Troubleshooting Proxmox Nodes, Containers, and VMs
[Nodes](#Nodes)

[Containers](#Containers)

---

# Nodes
## Shell
### E: Failed to fetch https://enterprise.proxmox.com/xxx/xxx ...
- Enterprise repos are still enabled on the Node. Disabling them will remove this error. An easy and safe way to do this is to run a post install script from Proxmox Helper Scripts (support to the project!). The script can be found at:
    - https://community-scripts.github.io/ProxmoxVE/scripts?id=post-pve-install

## Web UI
### The connection has timed out
1- Check that node is correctly connected to the router
  - Is the ethernet cable plugged into the node AND the router?
  - Is the ethernet cable damaged?
  - Is the router on?
  - Are you on the correct wifi network?
  - Are you connecting to the the right https address (ip and port)?

2- If everything looks good in Step 2: Reinstall Proxmox on the node

---

# Containers
### Bash: curl: command not found
There are 2 ways to resolve this, update the container or install curl.

- Update the container using:
  ```
  apt-get update && apt-get upgrade -y
  ```
  
  Note: the "-y" command will answer with a y or yes to all prompts from upgrade
  
- Install Curl
  ```
  apt install curl
  ```

### Container not connecting to internet
#### If Tailscales is running:
ping a DNS Server to check if it is a internet or DNS issue:

```
ping 8.8.8.8
```

<ins>**If ping 8.8.8.8 Worked = Possible DNS Issue**</ins>

```
ping google.com
```

**If ping 8.8.8.8 worked but ping google.com Failed = DNS Issue**

Why? 

The lxc is using the host's DNS setting that are set to Tailscale. Manually setting the DNS overrides those setting allowing the lxc to resolve its DNS and connect to the internet.)

- Go to the container and clink on DNS on left side
- Double click on "DNS Servers"
- Under DNS Server type 1.1.1.1 (or a DNS server option such as 8.8.8.8)
- ![Screenshot 2025-06-19 193911](https://github.com/user-attachments/assets/d55561ee-a2a7-46db-b6f6-a887966eceeb)

<ins>**If ping 8.8.8.8 Failed = No Internet**</ins>
- In the lxc click 'Network' on the left side panel
- Double click 'net0' line
- Set 'IPv4' to DHCP (Dynamic Host Configuration Protocol)

- ![image](https://github.com/user-attachments/assets/90d195d1-7a22-465c-9869-863e03eb5ce8)

- Click 'ok' to close


