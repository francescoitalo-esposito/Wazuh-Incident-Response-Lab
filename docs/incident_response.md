

# :shield:Incident Response Lifecycle – Wazuh SIEM Lab:shield:

This document details the full incident response workflow applied in the lab, covering Port Scanning (MITRE ATT&CK T1046) and SSH Brute Force (MITRE ATT&CK T1110), with evidence from Wazuh alerts and automated containment via Active Response.

---

## **🔎 Detection & Analysis**

### Port Scanning (MITRE ATT&CK: T1046 - Network Service Scanning)
- **Action:** Nmap SYN scan across low ports to identify exposed services.  
- **Command:**

  
   ```bash
  nmap -sS -p 1-1000 <target_ip>
*:mag_right:Observations:*
Sequential connection attempts detected; SSH exposed on TCP/22 with OpenSSH fingerprints.

*:pushpin:Evidence:* <img width="847" height="476" alt="1" src="https://github.com/user-attachments/assets/567823ed-1c0a-4883-adde-2c59ff9015a7" />

## **:closed_lock_with_key: Containment**            
*:desktop_computer:Port Scanning:*
Immediate block of attacker IP via firewall:


SSH Brute Force (MITRE ATT&CK: T1110 - Brute Force)
Action: Password spraying/brute force against SSH using rockyou.txt..

**Command:**
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://<target_ip>                      
*:mag_right:Observations:*
High volume of failed attempts; Wazuh correlates repeated authentication failures.

**Evidence:** <img width="1305" height="632" alt="33" src="https://github.com/user-attachments/assets/d86694bc-ba0f-453f-b36b-2af6c7538814" />
<img width="643" height="293" alt="2" src="https://github.com/user-attachments/assets/c6fa6a4e-57aa-4b90-93ec-194ed629a0d0" />

SSH Brute Force: Active Response configured in ossec.conf (XML):
<img width="1310" height="378" alt="44" src="https://github.com/user-attachments/assets/0163129e-21a0-4fef-99c1-f00b0cdb327a" />
Script (firewalldrop.sh):<img width="1301" height="584" alt="55" src="https://github.com/user-attachments/assets/22fcda5b-541f-49f7-9a26-2a6fa437d34c" />


## **🧹 Eradication**
Removed temporary firewall rules after validation.

Cleared blocked IPs once confirmed safe.

Updated Wazuh configuration to reduce false positives.

Validation: Confirm cessation of brute force alerts (rule.id 5760/5763).

## **🔄 Recovery**
Hardened firewall rules:
sudo ufw allow from 192.168.1.0/24 to any port 22
sudo ufw default deny incoming
sudo ufw logging on
Restricted SSH to admin subnet.

Enabled logging for suspicious connection attempts.

Verified legitimate SSH access still works.

## **:microscope: Lessons Learned**
Visibility: MITRE-aligned rules provided clear context (T1046, T1110).

Automation: Active Response minimized manual intervention and reduced MTTR.

Hardening: Firewall rules strengthened system resilience.

Process maturity: Full IR lifecycle applied with evidence and audit trail.

**Quick References**
MITRE ATT&CK: T1046 (Network Service Scanning), T1110 (Brute Force)

Key Wazuh rules observed:

Rule 5760 → sshd: authentication failed

Rule 5763 → sshd: brute force trying to get access

PAM anomalies → multiple failed logins in short period

