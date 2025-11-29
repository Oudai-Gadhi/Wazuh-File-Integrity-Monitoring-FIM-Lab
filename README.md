Cloud Security Monitoring Lab with Wazuh (AWS)

This project demonstrates how to deploy Wazuh SIEM on AWS and simulate real security attacks against an EC2 instance.
The goal is to show how Wazuh detects:

SSH brute‑force attempts

Privilege escalation commands (sudo abuse)

File Integrity Monitoring (FIM) changes

Rootcheck anomalies (trojaned system binaries)

All detections are visible in the Wazuh Dashboard.

🚀 Architecture Overview
Attacker Machine (Your PC)
         │
         ▼
   EC2 Wazuh Agent  ───────►  Wazuh Manager EC2
         │                       │
   SSH / File / Sudo attacks     │
         ▼                       ▼
  Wazuh FIM / SCA / Rootcheck   Dashboard UI


📸 PLACE ARCHITECTURE DIAGRAM HERE

🏗️ Environment Setup
Wazuh Manager (EC2)

Ubuntu 22.04

Wazuh Manager + Dashboard installed

Security Group exposes:

1514/TCP (agent logs)

55000/TCP (API communication)

Wazuh Agent (EC2)

Installed and registered with the manager

Connection confirmed:

sudo grep -i "connected" /var/ossec/logs/ossec.log

🔍 Configured File Integrity Monitoring (FIM)

Edited on the agent:

<syscheck>
  <disabled>no</disabled>
  <frequency>600</frequency>
  <scan_on_start>yes</scan_on_start>

  <directories check_all="yes">/home/ubuntu/my_dir</directories>
</syscheck>


Restart agent:

sudo systemctl restart wazuh-agent

🚨 Attack Simulations & Wazuh Detections

Below are the 3 main attacks executed, along with the alerts received.

🛑 Attack 1 — SSH Brute Force
▶️ Command Used

(From your attacking machine)

ssh ubuntu@<AGENT_PUBLIC_IP>
# Repeat incorrect passwords


📸 PLACE TERMINAL SCREENSHOT OF ATTACK HERE

✔️ Expected Wazuh Alerts

Multiple authentication failures

Brute force correlation rule

sshd: authentication failure
sshd: possible brute force attack detected
Rule: 5710 (level 5)
Rule: 5502 (level 10)


📸 PLACE WAZUH DASHBOARD SSH ALERTS HERE

🔼 Attack 2 — Privilege Escalation
▶️ Commands Used
sudo su -


or

sudo /bin/bash


📸 PLACE TERMINAL SCREENSHOT OF SUDO ATTACK HERE

✔️ Expected Wazuh Alerts
User ubuntu executed a command using sudo
Rule: 5402 (level 8)


📸 PLACE PRIVILEGE ESCALATION ALERT SCREENSHOT HERE

📁 Attack 3 — File Integrity Modification (FIM)

The monitored directory was:

/home/ubuntu/my_dir

▶️ Commands Used

Create or modify files:

echo "test 123" > /home/ubuntu/my_dir/testfile.txt
echo "changed again" >> /home/ubuntu/my_dir/testfile.txt


📸 PLACE TERMINAL SCREENSHOT OF FILE CHANGES HERE

✔️ Expected Wazuh Alerts
File added: /home/ubuntu/my_dir/testfile.txt
File modified: /home/ubuntu/my_dir/testfile.txt
Rule: 550 (level 7)


📸 PLACE FIM ALERT SCREENSHOT HERE

🪲 Bonus Detection — Rootcheck Anomalies

Wazuh automatically flagged suspicious system binaries:

Trojaned version of file '/bin/diff' detected
Trojaned version of file '/usr/bin/diff' detected
Rule: 510 (level 7)


📸 PLACE ROOTCHECK ALERT SCREENSHOT HERE

📝 Summary of Detections
Attack Category	Status	Description
SSH Brute Force	✅ Detected	Multiple failed logins, brute force rule
Privilege Escalation	✅ Detected	sudo elevation attempts logged
File Integrity Monitoring	✅ Detected	File created/modified alerts
Rootcheck	✅ Detected	Anomalous binaries flagged
🎯 What This Project Demonstrates

Deploying Wazuh SIEM in the cloud

Installing & registering Wazuh agents

Using FIM, Rootcheck, SCA, and SSH monitoring

Simulating real cyberattacks

Interpreting security alerts in a SIEM

Building a defensive cloud environment
