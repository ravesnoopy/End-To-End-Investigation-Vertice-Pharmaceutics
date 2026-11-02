# 02 · Attack timeline (UTC)

| # | Time (UTC) | Layer | What happened | Source |
|---|---|---|---|---|
| 1 | 1 Feb 14:18:20 – 14:19:16 | Mail | 9 e-mails to 9 recipients from `it-helpdesk@vertlce-salud.example` (lowercase **L** instead of **i**), subject "Accion requerida: tu acceso VPN vence hoy" | `vs-network` (mail.gateway) |
| 2 | 1 Feb 14:23:05 / 14:23:46 | Proxy | `r.duarte` clicks, then POSTs credentials to `/vpn/renew/submit` (302) from `BR-LT-0231`. Alert AL-1348 fires at 14:24:00 and is closed as false positive | `vs-network` (proxy.web) |
| 3 | 2 Feb 02:08:13 – 02:40:40 | VPN & MFA | 27 unapproved pushes (19 timeout, 8 denied) from `203.0.113.44` (NL) | `vs-auth` (idp.mfa) |
| 4 | 2 Feb 02:43:46 / 02:44:04 | VPN & MFA | Push approved; VPN opens with assigned IP `10.99.0.17`. AL-1340 fires at 02:44:30, closed as false positive | `vs-auth` |
| 5 | 2 Feb 02:58:41 | Directory | RDP logon (type 10) by `r.duarte` on `JMP-BR-01`, outside usual hours and hosts | `vs-auth` (windows.security) |
| 6 | 2 Feb 03:03:51 – 03:27:03 | Endpoint | Encoded PowerShell hunting for secrets; `whoami /priv`; `ms-settings` registry hijack; `fodhelper.exe`; first elevated process at 03:27:03 (T1548.002) | `vs-endpoint` |
| 7 | 2 Feb 03:21:08 → 9 Feb 03:07:59 | Network | DNS TXT beacon every ~300 s to `cdn-metrics-sync.example` (2,014 queries, 2,014 unique sub-domains) | `vs-dns` |
| 8 | 2 Feb 03:41:30 → 3 Feb 01:22:58 | Endpoint | Persistence via `svc32.exe`: scheduled task `MicrosoftEdgeUpdateTaskMachineCore2`, Run key `SvcUpdate`, local admin `svc_helpdesk` | `vs-endpoint` |
| 9 | 3 Feb 01:09:40 – 01:17:02 | Cloud | `aws sts` / `iam list-users`, then `create-access-key`; CreateAccessKey by `svc-etl-pipeline` for `svc-backup-legacy`, new key `AKIAVSBKLG00NEW0001`, no MFA, no ticket | `vs-endpoint`, `vs-cloud` |
| 10 | 6, 7, 8 Feb ~02:00 – 05:07 | Network + Cloud | 14.0 + 12.7 + 11.5 = **38.2 GB** to `203.0.113.90` (72 flows); 403 `GetObject` over 401 objects in `vs-patient-records-prod` | `vs-network`, `vs-cloud` |
| 11 | 9 Feb 03:10:05 – 03:10:33 | Endpoint | `wevtutil cl Security`, `wevtutil cl Microsoft-Windows-PowerShell/Operational`, event 1102. AL-1346 fires at 03:10:40 and is still open | `vs-endpoint` |

## Key durations
- POST → first exfiltrated data: **4 d 11 h 37 min 14 s** (1 Feb 14:23:46 → 6 Feb 02:01:00).
- VPN open → log wipe (lower bound): **7 d 26 min 1 s** (2 Feb 02:44:04 → 9 Feb 03:10:05).
- The time IT noticed the wipe is **not recorded** in any index, so that interval cannot be computed.

## Cheapest point to stop it
**1 Feb 14:24 UTC**: AL-1348 fired 14 seconds after the credentials were submitted. Resetting the password and killing sessions would have ended the attack before it began.
