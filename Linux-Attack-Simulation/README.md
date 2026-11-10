# Linux Attack Simulation and Log Forensics

This lab used a Linux server to examine SSH authentication, sudo sessions, local account creation, and a simulated data transfer. The investigation uses authentication logs and shell-history excerpts.

## Evidence review

| Evidence | What the screenshot shows |
| --- | --- |
| [SSH authentication](screenshots/brute-force.png) | Failed password attempts for `devops` from `10.0.2.2`, followed by an accepted password and session opening. |
| [Sudo activity](screenshots/privilege-escalation.png) | `devops` runs `id` and `bash` as root through sudo. |
| [Account creation](screenshots/backdoor-user.png) | A local account named `support1` is created and its password is changed. |
| [SSH login](screenshots/valid-login.png) | `support1` logs in from `10.0.2.2` at 15:29:16. |
| [Shell-history excerpt](screenshots/data-dump-and-exfiltration.png) | A staged file contains explicitly fake customer data. Recorded curl commands target `127.0.0.1:8080`. |
| [Account activity](screenshots/timeline-analysis.png) | Additional `support1` sessions and `devops` reading that account’s Bash history. |

The lab notes identify Hydra as the password-guessing tool. The authentication screenshot records the attempts and accepted login, without identifying the tool that generated them.

## Timeline supported by the screenshots

The log date is 7 December 2025. Times below use the recorded UTC+05:30 offset.

| Time | Recorded event |
| --- | --- |
| 15:26:26–15:26:48 | Failed SSH password attempts for `devops` |
| 15:26:53 | Accepted SSH password for `devops` and session opening |
| 15:27:19 | `devops` runs `id` as root through sudo |
| 15:27:24 | `devops` starts `bash` as root through sudo |
| 15:28:29 | Local account `support1` is created |
| 15:28:43 | Password changed for `support1` |
| 15:29:16 | Accepted SSH password for `support1` |
| 15:35:57 | Another accepted SSH login for `support1` |
| 17:27:32 | `devops` reads `/home/support1/.bash_history` through sudo |

The account-activity screenshot contains entries displayed out of timestamp order. This table sorts the selected events by their recorded time.

## MITRE ATT&CK mapping

The mappings below describe activities that could occur during an intrusion. They are interpretations of this controlled lab evidence.

| Activity | Mapping | Evidence limit |
| --- | --- | --- |
| Repeated SSH password attempts | [T1110.001 — Password Guessing](https://attack.mitre.org/techniques/T1110/001/) | Logs show failures and a later accepted password. |
| SSH access using an accepted password | [T1078 — Valid Accounts](https://attack.mitre.org/techniques/T1078/) | Successful authentication is visible. |
| Running a root shell through sudo | [T1548.003 — Sudo and Sudo Caching](https://attack.mitre.org/techniques/T1548/003/) | Sudo use is visible. The screenshots do not establish a sudoers misconfiguration or a software exploit. |
| Creating a local account | [T1136.001 — Local Account](https://attack.mitre.org/techniques/T1136/001/) | Creation of `support1` is visible. The screenshots do not establish that it was a backdoor or added to the sudo group. |

Reading shell history could map to [T1552.003 — Shell History](https://attack.mitre.org/techniques/T1552/003/) when it is used to seek credentials. Here, the visible command reads history during the investigation, so that mapping is conditional on intent.

## Limitations

The screenshots do not show creation of `support11`, a sudo-group change, or a completed transfer to another host. The shell-history excerpt shows curl commands targeting the loopback address and does not establish their execution time or success.

The available evidence supports a sequence of authentication and administrative events. Attribution of each event to attacker activity requires the original session context.
