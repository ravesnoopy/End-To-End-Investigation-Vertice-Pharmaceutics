# Changelog

## v1.1.0
- Executive summary now states **four** mis-closed alerts (AL-1340, AL-1341, AL-1342, AL-1348), consistent with E3.
- Exfiltration-volume detection re-mapped from T1041 to **T1048** (no evidence the traffic rides a C2 channel).
- `10.10.12.43` (MX-LT-0043) cross-referenced with the phishing click of `mkt-contractor-04` (click, no POST).
- Bucket exposure always reported as **62.2 %** (38.2 / 61.4 GB).
- `203.0.113.90` is labelled as the **exfiltration destination**; the C2 channel is the DNS beacon `cdn-metrics-sync.example`.
- Persistence (scheduled task, Run key, local admin) clearly separated from the beacon.
- Urgent actions now have an owner and a deadline.

## v1.0.0
- Initial release: nine exercises, four detections, IOC list and MITRE mapping.
