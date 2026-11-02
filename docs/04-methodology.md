# 04 · Methodology

## Principles
1. **Verify before asserting.** The easiest answer was often the wrong one.
2. **Everything in UTC.** One source (`vs-alertas-cdmx`) is in UTC−6 and would have hidden the link between alert and attack.
3. **Cross at least two sources.** Exfiltration was measured with firewall *and* cloud audit; they agree.
4. **Do not trust labels alone.** `labels.managed_by` and `labels.ticket` are self-reported. A ticket only supports an event if **host, user and time window** all match `vs-cambios`.
5. **Run a control query** for every headline number.
6. **Say what is unknown** and what evidence would settle it.

## Traps in the dataset
| Trap | How it was handled |
|---|---|
| Internal backup of ~60 GB per night to `10.10.30.5` | Allowed in the diagram, internal destination, constant pattern → not a finding |
| Scanner noise (73 % of alerts) | Excluded by user/IP, with a control query |
| Legitimate admin activity with tickets (CHG-1187, CHG-1190, CHG-1192) | Matched on host + user + window, not on label |
| Phishing simulation on 10 Feb (CHG-1193) | Ruled out: different server, authorised ticket, none of the attacker's IPs/accounts |
| Anonymous reads of `vertice-marketing-assets` (5.6 GB) | Not exfiltration (884 IPs, 3 weeks); separate finding: public bucket policy set without a ticket |
| CDMX alert export in local time | Converted to UTC |
| Shared cloud source IP | Distinguished by account, key and user agent |

## Workflow
1. Read the case file; set the absolute time range.
2. Hunt per layer (identity, endpoint, network, DNS, cloud).
3. Verify each closure of an alert against logs and change tickets.
4. Build the chain, measure the loss, document gaps.
5. Turn findings into detections and a dashboard.
