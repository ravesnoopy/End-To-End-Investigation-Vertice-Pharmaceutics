# 00 · Case file VSI-2027-014

## The company
Vértice Salud is a network of clinics and laboratories with sites in Mexico City, São Paulo and Santiago: about 120 user accounts, a cloud holding patient data, and a small SOC that handles alerts Monday to Friday.

## What happened
On **9 February 2027** IT noticed that the audit log of a jump server had been wiped. A quick review found three open, unassigned alerts. Management commissioned an independent assessment to answer three questions: was there an intrusion, how much was lost, and why did the SOC not stop it.

## Scope of the assessment
The task was **not** to contain the incident (it was over) but to:

1. Reconstruct what the attacker did, step by step.
2. Measure the damage.
3. Audit the SOC's work.
4. State honestly what cannot be determined.

## Data provided
An Elastic instance with ten data views and four weeks of telemetry (**18 Jan – 14 Feb 2027**):

| Data view | Content |
|---|---|
| `vs-auth` | Authentication, VPN, MFA |
| `vs-endpoint` | Processes and agent heartbeats |
| `vs-network` | Firewall, proxy and e-mail gateway |
| `vs-dns` | Resolver queries |
| `vs-cloud` | Cloud audit log |
| `vs-alerts` | SOC alert queue |
| `vs-alertas-cdmx` | CSV export of SOC alerts (**local time, UTC−6**) |
| `vs-inventario` | Asset inventory (maintained by hand) |
| `vs-cambios` | Change calendar (tickets) |
| `vs-diagrama` | Allowed flows from the network diagram |

## Ground rules
- Every claim must be backed by data that can be shown. "Cannot be determined" with a good argument beats an unsupported conclusion.
- All times in **UTC**. Sites work in UTC−6 (MX), UTC−3 (BR) and UTC−3 (CL, in February).
- All data is fictional: `.example` domains, documentation IP ranges, non-existent keys.
- Kibana's Security app is unavailable, so all detection is done with hand-written queries.
