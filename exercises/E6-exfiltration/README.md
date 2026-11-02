# E6 · How much was really taken
**Sources:** `vs-network` (firewall), `vs-cloud` (audit log). Two independent sources must agree.

<div align="center">
<img src="../../assets/screenshots/e6-firewall-vs-cloud.png" alt="Firewall vs cloud audit: both show 38.2 GB" width="80%">
</div>

| Q | Finding |
|---|---|
| 6.1 | Firewall, `JMP-BR-01` → `203.0.113.90`: **6 Feb 14.0 GB** (02:01:00–05:07:00, 24 flows) · **7 Feb 12.7 GB** (02:00:00–05:07:00, 24) · **8 Feb 11.5 GB** (02:02:00–05:05:00, 24). **Total 38.2 GB** (38,200,000,000 B, `network.bytes_out`), 72 flows. Windows are first/last recorded flow (no duration field). No other source sends to that IP. |
| 6.2 | **403 `GetObject`** by `svc-backup-legacy` with the new key on `vs-patient-records-prod`, all `success`, from `192.0.2.20`: 148 / 134 / 121 events and 14.0 / 12.7 / 11.5 GB per night. **Matches the firewall**. The cloud reads end ~15 min after the last flow; the data do not explain that. |
| 6.3 | The cloud source IP is **`192.0.2.20`**: the same IP behind **2,350 normal events from 7 accounts**, including the legitimate key rotation, so alerting on IP cannot tell theft from routine. The cloud log shows neither the originating host nor the destination: only the firewall does. `192.0.2.20` never appears in `vs-network`. What does distinguish the theft in the cloud: the **account**, the **new key** (no MFA, no ticket) and the **user agent** (Boto3 on Windows vs. aws-cli for the account's old key). |
| 6.4 | 38.2 GB of a 61.4 GB bucket = **62.2 %** (61.4 GB is the case figure; `vs-cloud` records reads, not bucket size). **403 reads over 401 distinct objects.** Two regulatory questions: (1) the bucket holds patient health data from MX, BR and CL, so is there a duty to notify each data-protection authority, and within what deadline? (2) Which patients, in which country, are in the 401 objects, to decide on notifying data subjects and on sanctions? Contents cannot be determined from `vs-cloud`. |
| 6.5 | **Not exfiltration.** 900 anonymous `GetObject` (5.6 GB, 581 objects, `media/` logos/brochures/banners/one clip) of `vertice-marketing-assets` from **884 distinct IPs**, 26 Jan → 14 Feb: public marketing material, not one actor. **The real finding:** on 26 Jan 15:12 UTC `mkt-contractor-04` ran `PutBucketPolicy` ("public read") from `198.51.100.140`, with no ticket in `vs-cambios`; anonymous reads start at 15:40. Review that nothing private sits in it and require approved tickets for bucket-policy changes. |
