# Intergrating Suricata and Wazuh in LXC Container
This is an install guilde for installing and intergrating Suricata and Wazuh in a Ubuntu LXC container on a Proxmox server.

Content Last Updated and Verified: 7/18/25

Suricata Website: https://suricata.io/

Wazuh Website: https://wazuh.com/

#### Important Note
This was first tried with Suricata 8.0.0 released on 7/8/25, but at the time of writing (7/18/25), that version was not working correctly in my lab and could not be intergrated. Suricata version 7.0.11 and Wazuh version 4.13 were used. 

# Table of Contents
[Versions Used](#Versions_Used)

[Install Suricata](#Install_Suricata)

[Install Wazuh](#Install_Wazuh)

[Test Intergration](#Test_Intergration)

[References](#References)

# Versions Used
Container Template: Ubuntu 22.04

Suricata: 7.0.11

Wazuh: 4.12

Proxmox: 8.4.0

# Install Suricata 
Install Suricata version 7.0.11 with the following command.

Run:
```
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:oisf/suricata-7.0
sudo apt install suricata jq
sudo apt install curl
```

Next check that the correct version was installed, version 7.0.11, and the that Suricata is running (active).

Check version and active status:
```
sudo suricata --build-info
sudo systemctl status suricata
```

For the most accurate results update the IP address in Suricata's yaml file to the container's IP.

In the container, run the following command to check the IP:
```
ip addr
```

Edit .yaml file:
```
sudo nano /etc/suricata/suricata.yaml
```

Find the line:
```
vars:
  address-groups:
    HOME_NET: "[192.168.0.XXX/8,....]"
    #HOME_NET: "[192.168.0.0/16]"
```

Delete all of the IP addresses in the first un-commented HOME_NET line brackets and replace them with the container's ip.

<img width="1114" height="582" alt="Screenshot 2025-07-16 175504" src="https://github.com/user-attachments/assets/9be960b6-a7e9-45e5-8320-a0fe29ad22f5" />

Install Suricata's rule set:
```
sudo suricata-update
```

Install Suricata's rule set for Wazuh:
```
curl -LO https://rules.emergingthreats.net/open/suricata-7.0.11/emerging.rules.tar.gz
sudo tar -xvzf emerging.rules.tar.gz && sudo mkdir /etc/suricata/rules && sudo mv rules/*.rules /etc/suricata/rules/
sudo chmod 640 /etc/suricata/rules/*.rules
```

Restart Suricata:
```
sudo systemctl restart suricata
```

Check the logs to make sure Suricata restarted correctly:
```
sudo tail /var/log/suricata/suricata.log
```

look a similar line at the end of the log:

[14432 - Suricata-Main] 2025-07-16 20:59:14 Notice: threads: Threads created -> W: 1 FM: 1 FR: 1   Engine started.

<img width="1458" height="582" alt="Screenshot 2025-07-16 180840" src="https://github.com/user-attachments/assets/edcdcb4d-12a6-4928-9b30-e8c1d6e328e7" />


# Install Wazuh
Install a Wazuh agent and start it.

In order for the Wazuh agent to access the Suricata logs and send them to the Wazuh Server, the agent's config file has to be modified.

Edit the Wazuh agent config file:
```
sudo nano /var/ossec/etc/ossec.conf 
```

Under the section "<!-- Log analysis -->" and right before "<!-- Active response -->", add the following lines:
```
  <localfile>
    <log_format>json</log_format>
    <location>/var/log/suricata/eve.json</location>
  </localfile>
```

<img width="1118" height="584" alt="Screenshot 2025-07-16 175430" src="https://github.com/user-attachments/assets/3097eb36-04eb-4541-8f50-6be00b259044" />

Restart the Wazuh agent:
```
sudo systemctl restart wazuh-agent
```

# Test Intergration
There are 2 tests to run to make sure that everything is working.

## Ping
Run a ping command to ping the Container. This test is from "Visualize the alerts" section from [Wazuh's Network IDS integration Guide](https://documentation.wazuh.com/current/proof-of-concept-guide/integrate-network-ids-suricata.html)

Send 20 pings to the container:
```
ping -c 20 "<Container IP>"
```

### Results
In the Wazuh Server dashboard, navigate to Threat Hunting. Filter by "rule.groups:suricata". There should be alerts from Suricata.

Example ping from Kali: 

Suricata: Alert - ET INFO Possible Kali Linux hostname in DHCP Request Packet

<img width="1918" height="860" alt="image" src="https://github.com/user-attachments/assets/c62f95d3-6c81-42c8-8933-7d58eee2d4ff" />

## Potentially Bad HTTP Traffic
Test for potentially bad traffic by calling a test website. This test is from "Alerting" section from [Suricata's Quickstart Guide](https://docs.suricata.io/en/suricata-7.0.11/quickstart.html#alerting)

Run in the Container:
```
curl http://testmynids.org/uid/index.html
sudo tail -f /var/log/suricata/fast.log
```

### Results
In the Terminal's log output the last line should look similar to:

07/18/2025-16:05:20.534857  [**] [1:2100498:7] GPL ATTACK_RESPONSE id check returned root [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 65.8.248.33:80 -> 192.168.0.115:48198

<img width="1298" height="624" alt="image" src="https://github.com/user-attachments/assets/d6446d11-bfd6-48e5-bd4e-07a235391537" />

The Wazuh Dashboard should show:

<img width="1919" height="860" alt="image" src="https://github.com/user-attachments/assets/939cf611-e17b-475a-8322-e4c3baf03fc1" />



# Troubleshooting

### SURICATA Ethertype unknown
<ins>Issue:</ins> 
- Suricata eve.json logs only showed "SURICATA Ethertype unknown"

<img width="1293" height="625" alt="Screenshot 2025-07-16 173758" src="https://github.com/user-attachments/assets/07515175-7999-4e0f-b98c-76b36f56b313" />

<ins>Fix:</ins>
- If Suricata is running version 8.0.0, downgrade to version 7.0.11


# References
Suricata Quickstart Guide- [https://docs.suricata.io/en/latest/quickstart.html](#https://docs.suricata.io/en/latest/quickstart.html)
Wazuh Network IDS integration- [https://documentation.wazuh.com/current/proof-of-concept-guide/integrate-network-ids-suricata.html](#https://documentation.wazuh.com/current/proof-of-concept-guide/integrate-network-ids-suricata.html)

