# 03 · Limits of the evidence

A report is only as good as its honesty about what the data cannot show.

| Question | Verdict | Why |
|---|---|---|
| Did the attacker access **FS-MX-02**? | **Cannot be determined** | Agent silent 5 Feb 22:00:47 → 6 Feb 08:00:42 (9 h 59 min 55 s), no ticket explains it, and it covers the whole first exfiltration night. No attacker processes or logons were seen, and the 7 flows during the gap are internal, but no flows exist between 01:38:19 and 07:35:00. |
| What was inside the 401 objects? | Unknown | `vs-cloud` records object names, not contents. |
| What is `192.0.2.20`? | Unknown | It never appears in `vs-network`; 7 accounts and 2,350 normal events share it. |
| What is `198.18.50.5`? | Unknown | Stable nightly pattern since the first day, no change ticket. No evidence it is theft. |
| What are `10.10.77.3` and `10.30.9.40`? | Hypothesis only | Ports 445, 9100 and 161 with 0.4–3 MB fit printers/monitoring; no logons or agent to confirm. |
| When did IT notice the wipe? | Not recorded | Only the date (9 Feb) is known. |
| What happened on JMP-BR-01 during its 13 h 59 min agent silence (9 Feb)? | Partially | The wipe events were still recorded; anything that left no trace in other sources is lost. |
| Why 44 encoded commands and not 46? | Unexplained | Variants of the parameter name are a lead to try. |

## What *can* still be reconstructed after the wipe
Events had already been shipped to the SIEM, and other sources are independent of the server: `vs-auth` (VPN, RDP), `vs-network` (proxy, firewall), `vs-dns` (beacon) and `vs-cloud`.
