# Suricata Install and Troubleshooting
Content Last Updated and Verified: 7/18/25

Website: https://suricata.io/

# Table of Contents
[Install Most Current Version](#Install_Current_Version)

[Install Previous Version](#Install_Previous_Version)

[Troubleshooting](#Troubleshooting)

# Install Current Version
Use the following guide to download Suricata in a lxc container.

Container Template: Ubuntu 22.04

Guide: https://docs.suricata.io/en/latest/quickstart.html

# Install A Previous Version 
Use the following guide to download Suricata in a lxc container with a small mod to the version command.

Container Template: Ubuntu 22.04

Guide: https://docs.suricata.io/en/latest/quickstart.html

The command given in step "2.1. Installation" will install the current stable version. To install a specific version, the second line of the command has to be modified. For example, to install Suricata ver. 7.0.11 you would use the command below. 

```
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:oisf/suricata-7.0
sudo apt update
sudo apt install suricata jq
```

What changed?

The end of the second line changed from "suricata-stable" to "suricata-7.0". To install Suricata ver. 6 and so on, all you need to do is changed the number to the desired version.


# Troubleshooting

### Step 2.5 Alerting
When running the trigger below there can be 2 issues, No responce/output and log Output showing "SURICATA AF-PACKET truncated packet".


Trigger:

```
sudo tail -f /var/log/suricata/fast.log
curl http://testmynids.org/uid/index.html
```

---

#### 1- No Responce/Output
- Wait a minute then ctrl+C to cancel
- If error 'Curl: command not found' run:
    ```
    apt install curl
    ```

---

#### 2- log Output showing "SURICATA AF-PACKET truncated packet"
Expected result: alert ip any any -> any any (msg:"GPL ATTACK_RESPONSE id check returned root"; content:"uid=0|28|root|29|"; classtype:bad-unknown; sid:2100498; rev:7; metadata:created_at 2010_09_23, updated_at 2010_09_23;)

Actual result: 06/20/2025-15:30:29.415984  [\*\*] [1:2200122:1] SURICATA AF-PACKET truncated packet [\*\*] [Classification: Generic Protocol Command Decode] [Priority: 3] [\*\*] [Raw pkt: AA 00 00 CC F0 ... ]

<ins>**What is happening?**</ins>
  - A truncated packet means that only a portion of a packet is captured, not the whole thing.

## This did NOT fix the issue
<ins>**Attempted Fix**</ins>
  - Edit the config file
  ```
  nano /etc/suricata/suricata.yaml
  ```
  - Find the 'af-packet:' section
    - ![image](https://github.com/user-attachments/assets/563b7481-a2d3-4532-8780-72cd64b7f4ab)
  - Find '- interface:' at the bottom of the section
    - ![Screenshot 2025-06-20 120728](https://github.com/user-attachments/assets/70f154a1-8577-4bae-b8ad-614eec42f12f)
  - Delete the '#' in front of 'threads: auto'
  - Save and exit the file
  - reboot
  - Rerun the trigger
