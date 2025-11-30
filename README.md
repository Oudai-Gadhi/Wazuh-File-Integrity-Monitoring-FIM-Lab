# Wazuh File Integrity Monitoring (FIM) Lab

This project demonstrates how to set up a **Wazuh Manager** and **Wazuh Agent** on two EC2 instances, configure File Integrity Monitoring, execute attacks, and observe events in the Wazuh Dashboard.

You can add your screenshots in the dedicated sections.

---

## 📌 1. Architecture

* **Wazuh Manager** on EC2 (private subnet)
* **Wazuh Agent** on EC2 (private subnet)
* Communication via TCP/1514
* Monitoring a custom directory on the agent: `/home/ubuntu/my_dir/`

---

## 📌 2. Wazuh Manager Setup

1. Installed Wazuh Manager via official repository.
2. Verified service:

   ```bash
   sudo systemctl status wazuh-manager
   ```
3. Added the Agent key to the manager:

   ```bash
   sudo /var/ossec/bin/manage_agents
   ```
4. Checked manager logs:

   ```bash
   sudo tail -f /var/ossec/logs/ossec.log
   ```

---

## 📌 3. Wazuh Agent Setup

1. Installed Wazuh Agent on the second EC2.
2. Registered the agent with the manager.
3. Confirmed connection:

   ```bash
   grep -i "connected" /var/ossec/logs/ossec.log
   ```
4. Output example:

   > Agent successfully connected to the server (10.0.1.32:1514/tcp)

---

## 📌 4. File Integrity Monitoring Configuration

FIM was configured in:

```
/etc/ossec/etc/ossec.conf
```

### Added monitoring rule:

```xml
<directory check_all="yes" realtime="yes">/home/ubuntu/my_dir</directory>
```

### Reloaded agent:

```bash
sudo systemctl restart wazuh-agent
```

### Verified FIM initialization:

```bash
grep "my_dir" /var/ossec/logs/ossec.log
```

---

## 📌 5. Simulated Attacks 
Executed SSH brute force attack :
```bash
hydra -l ubuntu -P wordlist.txt ssh://<IP>
```
Executed priv escalataion Method and created new user :
```bash
sudo su
```
```bash
useradd oudai
echo "hello" > /tmp/.hidden
```


Executed the following actions inside `/home/ubuntu/my_dir`:

### ✔️ Create file

```bash
echo "test content" > testfile.txt
```

### ✔️ Modify file

```bash
echo "new content" >> testfile.txt
```

### ✔️ Changing  file permissions

```bash
chmod 600 testfile.txt
```

---

## 📌 6. Events in Wazuh Dashboard

Wazuh Dashboard captured:
* SSH brute force attack detection
* Privelege escalation and persistence by user creation
* File creation alerts
* File modification alerts
* File deletion alerts
* Permissions/ownership changes 

👉 **ALL needed screenshots are under /screenshots**


## 📌 7. Conclusion

This project demonstrates:

* Deployment of Wazuh in a cloud environment
* Successful agent‑manager communication
* Real-time File Integrity Monitoring
* Detection of attacks through Wazuh alerts

This forms the base for future security monitoring additions including:

* CloudTrail
* Sysmon
* Log enrichment
* Custom rules

---

Prepared by: **Oudai Gadhi**
