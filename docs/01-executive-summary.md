# 01 · Executive summary (for the CISO, ≤ 200 words)

**What happened.** On 1 February an employee in Brazil typed their password into a fake VPN-renewal page. On 2 February the attacker flooded their phone with 27 approval requests until one was accepted, then logged in. Over the next week the attacker returned seven times, worked from a jump server nobody had inventoried, created a new cloud access key and, on 6–8 February, copied 38.2 GB. Finally, they wiped the server's security log.

**How much was lost.** 38.2 GB of the 61.4 GB patient-records store: **62.2 %**.

**What is still unknown.** Which exact files were copied; whether the Mexico City file server was touched (its monitoring agent went silent for almost 10 hours); when IT first noticed.

**Why it was not stopped.** Four alerts tied to the attack were closed as false positives; three more were left open and unassigned.

**Three urgent actions**
1. Isolate the jump server `JMP-BR-01`; rotate the compromised password and the keys of `svc-backup-legacy` and `svc-etl-pipeline`. **Owner: IT Security · 24 hours.**
2. Require number-matching MFA and a second reviewer before any phishing or credential alert can be closed. **Owner: SOC lead · 7 days.**
3. Start the legal assessment of breach notification to regulators and patients in Mexico, Brazil and Chile. **Owner: Legal with the CISO · 72 hours.**
