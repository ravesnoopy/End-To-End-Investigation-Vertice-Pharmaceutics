# E4 · Hunting directly in the logs
**Sources:** `vs-auth`, `vs-endpoint`, `vs-cloud`, `vs-dns`, `vs-cambios`

<div align="center">
<img src="../../assets/screenshots/e4-mfa-fatigue.png" alt="MFA fatigue burst for r.duarte" width="80%">
</div>

| Q | Finding |
|---|---|
| 4.1 | **`r.duarte`**: **27 unapproved MFA pushes** (19 timeout, 8 denied), all on 2 Feb between 02:08:13 and 02:40:40 UTC. Other users have 1–2 scattered pushes. Totals in `vs-auth`: 59 unapproved (46 timeout, 13 denied). |
| 4.2 | Source `203.0.113.44`, country **NL**. Window 02:08:13–02:40:40; the only approved push from that IP: **02:43:46**. VPN opened **02:44:04** with pool IP **`10.99.0.17`**. 7 VPN connections from that IP in total (2–9 Feb), all with `10.99.0.17`. Baseline of the user: `198.18.32.180` (BR) only. |
| 4.3 | **7 anomalous RDP logons** by `r.duarte` on JMP-BR-01 from `10.99.0.17`: 2 Feb 02:58:41 · 3 Feb 00:58:50 · 5 Feb 19:43:10 · 6 Feb 01:41:42 · 7 Feb 01:45:00 · 8 Feb 01:44:35 · 9 Feb 02:51:52. Six fall between 21:58 and 23:58 Brazil time; the 5 Feb one (16:43 local) is anomalous for host and IP. Valid password and MFA are not enough: the MFA was approved by the attacker after 27 pushes, the IP/country are new, the user had never entered JMP-BR-01 (only `BR-LT-0231`, `DC-BR-01`), and is a Billing user, not an admin. The 10 logons of `l.campos` from `10.20.12.41` are normal. |
| 4.4 | **3** encoded PowerShell commands on JMP-BR-01, all `r.duarte`, parent `cmd.exe`, no labels. Decoded (Base64, UTF-16LE): ① 2 Feb 03:03:51 `Get-ChildItem E:\etl,D:\jobs -Recurse -Include *.py,*.ps1,*.cfg \| Select-String -Pattern 'key','secret','token'…` (credential hunt) ② 3 Feb 01:09:40 `aws sts get-caller-identity --profile etl; aws iam list-users --profile etl` ③ 3 Feb 01:16:33 `aws iam create-access-key --user-name svc-backup-legacy --profile etl`. |
| 4.5 | `vs-endpoint` returns **44** `-EncodedCommand` commands (the brief mentions 46; the 2-command gap is unexplained). Excluding SCCM (32, all SYSTEM) and ticketed (9, CHG-1187, `j.herrera`) leaves **3**: the attacker's. **Risk:** labels are self-reported and forgeable; a ticket supports a command only if host, user and window match `vs-cambios`. |
| 4.6 | Malicious **CreateAccessKey**: 3 Feb **01:17:02 UTC**, actor `svc-etl-pipeline` (key `AKIAVSETL00OLD0001`, aws-cli on Windows, no MFA, no ticket), for **`svc-backup-legacy`**, new key **`AKIAVSBKLG00NEW0001`**. It follows the PowerShell command by 29 s. The legitimate one is 4 Feb 15:00:20 (`a.morales` → `svc-reporting`, MFA, CHG-1190). |
| 4.7 | Beacon: **`cdn-metrics-sync.example`**, host **JMP-BR-01** (`10.20.50.11`), type **TXT**, every **300 s** (±~10 s), 2 Feb 03:21:08 → 9 Feb 03:07:59, **2,014 queries / 2,014 unique sub-domains** (hex strings, 32–44 chars). Not malicious: `healthcheck.monitor-ti.example` (2 app servers, fixed name, since 18 Jan) and `telemetry.office.example` (112 laptops, ~60 queries each, fixed name, since day one). |
| 4.8 | Persistence on JMP-BR-01, all `r.duarte`, binary `C:\ProgramData\Microsoft\Crypto\svc32.exe`: ① 2 Feb 03:41:30 scheduled task `MicrosoftEdgeUpdateTaskMachineCore2` (`/sc onstart /ru SYSTEM`) ② 2 Feb 03:48:12 Run key `SvcUpdate` ③ 3 Feb 01:22:40 local account `svc_helpdesk`, added to Administrators 01:22:58. **Contrast** with `l.campos` on `APP-BR-01`, 10 Feb: task `VS-LogRotate`, service `VSAgentProxy`, user `svc-vsagent` in Administrators. All carry ticket **CHG-1192** (approved, same host, 13:00–14:00 UTC, events 13:05–13:20), binaries in `D:\jobs` / `Program Files`, midday, owner is the actor. The attacker acted at night, no ticket, a path that imitates Microsoft, a task name that imitates Edge Update. |
| 4.9 | **UAC bypass via `fodhelper.exe` (T1548.002)**, 2 Feb: 03:24:10 `whoami /priv` → 03:26:45 `reg add HKCU\Software\Classes\ms-settings\Shell\Open\command` → 03:27:02 `fodhelper.exe` → **03:27:03 first elevated process** (`cmd.exe … svc32.exe --elevated`, parent `fodhelper.exe`). 5 elevated processes in total, all on JMP-BR-01. |

## Open lead
The 44 vs 46 mismatch: try parameter variants (`-e`, `-ec`, other casing) before leaving it as pending.
