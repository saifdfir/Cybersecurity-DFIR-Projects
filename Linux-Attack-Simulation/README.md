# 🔐 Linux Attack Simulation & Log Forensics  
**Digital Forensics & Incident Response Case Study**

This project simulates a real-world intrusion on a Linux server and documents a full DFIR investigation.  
The goal is to demonstrate practical skills in **log analysis**, **attack reconstruction**, and **MITRE ATT&CK mapping**.

---

## 📌 Scenario Summary

A Linux server was compromised using SSH brute force, followed by privilege escalation, persistence creation, and an attempted data exfiltration.

This documentation covers:

1. **Red Team Attack Simulation**
2. **Blue Team Log Investigation**
3. **Timeline Reconstruction**
4. **MITRE ATT&CK Mapping**
5. **Screenshots & Evidence Artifacts**

---

## 🔴 1. Attack Simulation (Red Team)

### **1️⃣ SSH Brute Force Attack**
A brute-force attempt was carried out against SSH using Hydra to guess valid credentials.

**MITRE ATT&CK:** `T1110 – Brute Force`

📸 *Screenshot:* `screenshots/brute-force.png`

---

### **2️⃣ Valid Credential Login**
After repeated attempts, the attacker successfully logged in using the compromised account:

- Username: `support1`
- Source IP: `10.0.2.2`

**MITRE ATT&CK:** `T1078 – Valid Accounts`

📸 *Screenshot:* `screenshots/valid-login.png`

---

### **3️⃣ Privilege Escalation Attempt**
The intruder attempted to escalate privileges using sudo misconfiguration:

- Viewed root’s bash history  
- Executed commands requiring elevated permissions  

**MITRE ATT&CK:** `T1068 – Exploitation for Privilege Escalation`

📸 *Screenshot:* `screenshots/privilege-escalation.png`

---

### **4️⃣ Persistence: Backdoor User Creation**
A new local user (`support11`) was created and added to the sudo group.

This is a common persistence technique after initial compromise.

**MITRE ATT&CK:** `T1098 – Account Manipulation`

📸 *Screenshot:* `screenshots/backdoor-user.png`

---

### **5️⃣ Data Exfiltration Attempt**
The attacker prepared data (`fake_dump.sql`) and attempted to exfiltrate it using:

- `curl` (HTTP exfiltration)
- `nc` (netcat transfer)

**MITRE ATT&CK:**  
- `T1537 – Transfer Data to Cloud Account` (HTTP POST)  
- `T1041 – Exfiltration Over C2 Channel`  

📸 *Screenshot:* `screenshots/data-dump-and-exfiltration.png`

---

## 🔵 2. Blue Team Investigation (Forensics)

### **Log Sources Used**
- `/var/log/auth.log` → authentication attempts, ssh access  
- `bash_history` → attacker activity  
- Timestamps from multiple sessions  

### **Key Findings**
- Numerous failed SSH attempts preceding the compromise  
- Successful login from IP `10.0.2.2`  
- Multiple session openings for user `support1`  
- Sudo privilege attempts  
- Creation of a new user account (`support11`)  
- Access to `.bash_history` for reconnaissance  

📸 *Screenshot:* `screenshots/timeline-reconstruction.png`  
(command: `grep -E "support1|support11" /var/log/auth.log`)

---

## 🕒 3. Timeline Reconstruction (Summary)

| Time (IST) | Event |
|-----------|--------|
| 15:28:29 | Brute-force attempts begin |
| 15:35:57 | Successful SSH login for `support1` |
| 15:36–15:38 | Multiple sessions opened |
| 15:39 | Privilege escalation attempts |
| 15:40 | `.bash_history` accessed |
| 15:41 | New user `support11` created |
| 15:42+ | Data exfiltration commands executed |

This matches the attacker workflow:  
**Initial Access → Privilege Escalation → Persistence → Exfiltration**

---

## 🧩 MITRE ATT&CK Mapping

| Stage | Technique | ID |
|-------|-----------|------|
| Initial Access | Brute Force | **T1110** |
| Initial Access | Valid Accounts | **T1078** |
| Privilege Escalation | Exploit Privilege Misconfig | **T1068** |
| Persistence | Account Manipulation | **T1098** |
| Discovery | Read Bash History | **T1083** |
| Exfiltration | Data Transfer | **T1041 / T1537** |

---

## 🧾 Conclusion

I reviewed the authentication records, sudo activity, account changes, and shell-history output from this lab. I organized the recorded events into a timeline and mapped them to MITRE ATT&CK.
