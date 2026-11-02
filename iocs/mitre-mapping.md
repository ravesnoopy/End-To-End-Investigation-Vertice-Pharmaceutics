# MITRE ATT&CK mapping

| Tactic | Technique | Evidence | Detection |
|---|---|---|---|
| Initial Access | T1566.002 Spearphishing Link | 9 e-mails from `vertlce-salud.example` | – |
| Credential Access | T1621 MFA Request Generation | 27 pushes from `203.0.113.44` | `detections/mfa-fatigue.esql` |
| Initial Access / Persistence | T1078 Valid Accounts | VPN + RDP as `r.duarte` | – |
| Execution | T1059.001 PowerShell | 3 encoded commands | `detections/powershell-encoded.esql` |
| Defense Evasion | T1027 Obfuscated Files or Information | Base64 UTF-16LE payloads | `detections/powershell-encoded.esql` |
| Privilege Escalation | T1548.002 Bypass UAC | `fodhelper.exe` + `ms-settings` hijack | – |
| Persistence | T1053.005 Scheduled Task | `MicrosoftEdgeUpdateTaskMachineCore2` | – |
| Persistence | T1547.001 Registry Run Keys | `SvcUpdate` | – |
| Persistence | T1136.001 Create Local Account | `svc_helpdesk` | – |
| Command & Control | T1071.004 DNS | `cdn-metrics-sync.example` TXT / 300 s | – |
| Persistence (cloud) | T1098.001 Additional Cloud Credentials | `AKIAVSBKLG00NEW0001` | `detections/create-access-key.esql` |
| Collection | T1530 Data from Cloud Storage | 403 `GetObject` | – |
| Exfiltration | T1048 Exfil Over Alternative Protocol | 38.2 GB to `203.0.113.90` | `detections/exfil-volume.esql` |
| Defense Evasion | T1070.001 Clear Windows Event Logs | `wevtutil cl Security` | – |

Techniques without a detection are candidates for follow-up work.
