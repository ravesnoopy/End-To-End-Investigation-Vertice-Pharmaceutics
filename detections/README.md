# Detections

| File | Technique | Fires on | Does **not** fire on |
|---|---|---|---|
| `mfa-fatigue.esql` | T1621 | 27 failed pushes in 1 hour | Users with 1–2 scattered failures |
| `powershell-encoded.esql` | T1059.001 · T1027 | 3 commands on JMP-BR-01 | SCCM jobs; CHG-1187 only if host + user + window match |
| `create-access-key.esql` | T1098.001 | Key for `svc-backup-legacy` | CHG-1190 rotation (user + target + window) |
| `exfil-volume.esql` | T1048 | 3 nights, 38.2 GB | Internal ~60 GB nightly backup |

Paste into Kibana → Discover → ES|QL, with an absolute time range. Tune thresholds to your own baseline before using in production.
