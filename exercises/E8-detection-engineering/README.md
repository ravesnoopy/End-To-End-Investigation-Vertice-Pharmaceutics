# E8 · Detections that would have worked

Four ES|QL detections that fire on the attack and not on the legitimate activity in this case. The ready-to-use files live in [`/detections`](../../detections/).

| # | Detection | Rows returned | Threshold / criterion | Legit activity avoided | MITRE |
|---|---|---|---|---|---|
| 8.1 | MFA fatigue | **1** (`r.duarte`, 2 Feb 02:00, 27 failures, 1 IP) | ≥ 5 unapproved pushes per user per hour | 32 isolated pushes of other users (1–2 each) | T1621 |
| 8.2 | Encoded PowerShell | **3** (all `r.duarte` on JMP-BR-01) | Exclude SCCM/SYSTEM; exclude CHG-1187 **only if** user + host + window match | 41 of 44 encoded commands (32 SCCM + 9 CHG-1187) | T1059.001 · T1027 |
| 8.3 | CreateAccessKey | **1** (3 Feb 01:17:02, `svc-etl-pipeline` → `svc-backup-legacy`) | Exclude CHG-1190 **only if** user, target account and window match | Rotation of `svc-reporting` on 4 Feb 15:00:20 | T1098.001 |
| 8.4 | Egress volume | **3** (JMP-BR-01 → 203.0.113.90: 14.0 / 12.7 / 11.5 GB) | ≥ 5 GB per source, destination and day to non-private IPs | ~60 GB/night internal backup (private destination, allowed in diagram) | **T1048** |

## Design notes
- **Never trust a label alone.** Exclusions require that ticket, user, host and time window all match the change calendar.
- **8.1 limit:** a fixed 1-hour bucket could split a burst across two hours (not the case here).
- **8.4 limits:** a theft to a private destination, or under 5 GB/day, would not fire. The rule detects volume, not destination reputation: pair it with a known-external-destinations list. The 5 GB threshold leaves more than 2× margin under the smallest night (11.5 GB); the upper bound was not verified with a control query.
- **MITRE change:** T1048 (not T1041), as no evidence shows the traffic travelling over a C2 channel.
- **8.2 open lead:** the dataset returns 44 encoded commands while the brief says 46; try `-e`, `-ec` and other casings.
