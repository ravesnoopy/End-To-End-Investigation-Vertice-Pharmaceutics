# E3 · The alerts that did fire
**Sources:** `vs-alerts`, `vs-alertas-cdmx`, `vs-cambios`, `vs-auth`, `vs-endpoint`, `vs-cloud`, `vs-network`

| Q | Finding |
|---|---|
| 3.1 | **348 alerts**, **254 (73.0 %)** from the vulnerability scanner (`svc-vulnscan`, `VULN-SCAN-01`, `10.20.5.15`). Only **94 are not scanner-generated**: nearly three in four alerts are noise, so closing by habit becomes likely. |
| 3.2 | **3 open**: `AL-1343` (6 Feb 04:31:00, *Large outbound transfer to rare destination*, medium), `AL-1346` (9 Feb 03:10:40, *Audit log cleared*, high), `AL-1347` (9 Feb 03:20:40, *Cloud API access denied burst*, medium). Status totals: 342 closed FP, 3 open, 2 closed duplicate, 1 closed TP. |
| 3.3 | **Four attack alerts closed as false positive:** AL-1348, AL-1340, AL-1341, AL-1342. |
| 3.4 | Why each closure was wrong: see table. |
| 3.5 | `AL-1340` appears as 2027-02-01 20:44:30 in `vs-alertas-cdmx` (UTC−6). Real time: **2027-02-02 02:44:30 UTC**. Uncorrected, it would look ~5.5 h *before* the attack's first pushes (02:08 UTC) and be discarded. It actually fired 26 s after the VPN opened. |
| 3.6 | Destination `203.0.113.90`. `AL-1343/1344/1345` (6, 7, 8 Feb) are three different nights (14.0, 12.7, 11.5 GB). Two were closed as "duplicate of the alert of the 6th" and the original was **never investigated** (still open). |

## 3.4 · Wrong closures
| Alert | Closed by | Stated reason | Why it is wrong |
|---|---|---|---|
| AL-1348 · 1 Feb 14:24:00 | s.vega | No download seen | Proxy shows `POST /vpn/renew/submit` → 302 at 14:23:46 to `vertlce-salud.example` (`198.51.100.201`). **Credential capture**, not a download. |
| AL-1340 · 2 Feb 02:44:30 | m.ortiz | User confirmed by chat | ≥ 25 pushes (02:08–02:38) and the approval (02:43:46) came from `203.0.113.44` (NL), never seen for this user (baseline: `198.18.32.180`, BR). No chat confirmation in any log. VPN opened 02:44:04 with `10.99.0.17`. |
| AL-1341 · 2 Feb 03:04:20 | s.vega | Covered by CHG-1187 | CHG-1187: `j.herrera`, 4 Feb, on JMP-MX-01 / APP-MX-01 / FS-MX-02 / SQL-MX-01. Alert: `r.duarte`, **2 Feb**, on **JMP-BR-01**, no ticket label. |
| AL-1342 · 3 Feb 01:17:30 | m.ortiz | Covered by CHG-1190 | CHG-1190: rotation of `svc-reporting` by `a.morales`, 4 Feb 15:00–16:00, with MFA. The alerted event: `svc-etl-pipeline` created a key for `svc-backup-legacy` on 3 Feb, no MFA, no ticket. |
