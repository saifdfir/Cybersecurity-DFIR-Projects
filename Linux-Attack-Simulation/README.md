# Linux Attack Simulation and Log Forensics

I simulated an intrusion on a Linux server and used authentication logs and shell history to reconstruct it. The exercise covered SSH access, sudo activity, creation of a second account, and an attempted data transfer.

## Attack simulation

| Step | What happened | Evidence |
| --- | --- | --- |
| SSH brute force | Hydra tried to guess valid SSH credentials. | [Failed attempts](screenshots/brute-force.png) |
| Successful login | The account `support1` logged in from `10.0.2.2` after repeated failures. | [Login record](screenshots/valid-login.png) |
| Privilege escalation attempt | The exercise used a sudo misconfiguration to run commands requiring elevated permissions and view root’s Bash history. | [Sudo activity](screenshots/privilege-escalation.png) |
| Persistence | A second local account, `support11`, was created and added to the sudo group. | [Account creation](screenshots/backdoor-user.png) |
| Data transfer attempt | The exercise staged `fake_dump.sql` and attempted to transfer it using `curl` and `nc`. | [Transfer commands](screenshots/data-dump-and-exfiltration.png) |

## Investigation

I used `/var/log/auth.log` to trace failed and successful SSH logins, session openings, sudo activity, and account creation. I used shell history to check which commands ran. Searching the authentication log for `support1` and `support11` helped correlate the activity of the two accounts.

[View the timeline evidence](screenshots/timeline-analysis.png)

| Time (IST) | Event |
| --- | --- |
| 15:28:29 | Brute-force attempts begin |
| 15:35:57 | Successful SSH login for `support1` |
| 15:36–15:38 | Multiple sessions open |
| 15:39 | Privilege escalation attempts |
| 15:40 | Bash history is accessed |
| 15:41 | The `support11` account is created |
| 15:42 onward | Data transfer commands run |

## MITRE ATT&CK mapping

These are the technique IDs recorded in the lab notes. The mappings are interpretations of the observed activity.

| Stage | Activity | Recorded ID |
| --- | --- | --- |
| Initial access | SSH brute force | T1110 |
| Initial access | Login with valid credentials | T1078 |
| Privilege escalation | Sudo misconfiguration | T1068 |
| Persistence | Account and group changes | T1098 |
| Discovery | Bash history access | T1083 |
| Exfiltration | Attempted data transfer | T1041 / T1537 |
