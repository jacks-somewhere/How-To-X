# Local Port Fowarding for Remote Web UI Access
To access the Web UI of a service running on a remote device you have to setup ssh local port fowarding.

From your remote computer (the one that you are on) start a ssh connection to the device running the service and enter the password:

```
ssh -L 3120:<WebUI_IP>:443 <username>@<IP_connectingto>
```

  - '-L' Specifies the username and IP to log into
  - '3120:\<WebUI_IP\>:443' tells the host to foward all traffic recived on port 8806 to port 443 (the Web UI)
  - \<username\>@\<tailscaleip\> is the username and ip of the tailscale device

To connect to the Web UI, in your browser go to:

```
http://localhost:3120
```
