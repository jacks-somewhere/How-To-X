# Wazuh Server and Agent Install
"Wazuh is a security platform that provides unified XDR and SIEM protection for endpoints and cloud workloads. The solution is composed of a single universal agent and three central components: the Wazuh server, the Wazuh indexer, and the Wazuh dashboard." [Wazuh Quickstart Quide](https://documentation.wazuh.com/current/quickstart.html)

[Install Server](#Install_Server)

[Troubleshooting](#Troubleshooting)

# Install Server
Follow the quickstart guide on Wazuh: [https://documentation.wazuh.com/current/quickstart.html](https://documentation.wazuh.com/current/quickstart.html)

# Install Agent
Follow the Quickstart guide on Wazuh: [https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html)

# Troubleshooting
### Ubuntu 22.04 Container: Dependency Problems Error while Installing Endpoint Agent
**Error:**  
Reading database ... 19955 files and directories currently installed.)  
Preparing to unpack .../wazuh-agent_4.12.0-1_amd64.deb ...  
Unpacking wazuh-agent (4.12.0-1) over (4.12.0-1) ...  
dpkg: dependency problems prevent configuration of wazuh-agent:  
 wazuh-agent depends on lsb-release; however:  
  Package lsb-release is not installed.  

dpkg: error processing package wazuh-agent (--install):  
 dependency problems - leaving unconfigured  
Errors were encountered while processing:  
 wazuh-agent  

**Fix:**  
- Install a missing package  
```
apt-get update
apt-get install lsb-release
```
- run install agent command again
