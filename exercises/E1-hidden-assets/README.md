# E1 · Hidden assets
**Sources:** `vs-inventario`, `vs-network`, `vs-endpoint`, `vs-auth`

Assets that exist but are not in the inventory are, by definition, the ones nobody protects.

| Q | Finding |
|---|---|
| 1.1 | **5 internal IPs** generate traffic in `vs-network` but are not in the inventory: `10.20.50.11` (JMP-BR-01), `10.10.12.43` (MX-LT-0043), `10.10.77.3`, `10.20.40.7` (ETL-LEGACY-02), `10.30.9.40`. |
| 1.2 | Two of them have an agent in `vs-endpoint`: **JMP-BR-01** and **ETL-LEGACY-02**. `vs-endpoint` has no IP field, so they were matched on `host.name`. |
| 1.3 | **JMP-BR-01** concentrates the attack. *Auth:* 7 RDP (type 10) logons by `r.duarte` from VPN IP `10.99.0.17`, 2–9 Feb, mostly overnight UTC. *Endpoint:* 3 encoded PowerShell commands, 2 Feb 03:03:51 → 3 Feb 01:16:33. *Network:* 72 outbound flows to `203.0.113.90`, 38.2 GB. |
| 1.4 | See table below. |
| 1.5 | **6 inventory entries** generate no traffic: `OLD-SRV-01…06` (`10.10.60.10–.15`, Windows Server 2012, CDMX, criticality Medium, owner IT). The inventory is unreliable in **both** directions: 5 live assets missing, 6 idle assets listed. They may be decommissioned, powered off or outside log coverage; the data cannot say which. |

## 1.4 · The four non-attack hidden assets
| Asset | What it is | In the attack? | How it was determined |
|---|---|---|---|
| `10.20.40.7` | **ETL-LEGACY-02**, uninventoried server running a nightly `svc-etl-pipeline` job; one flow per night to `198.18.50.5:443` (≈16.1 GB total) | **No**: it never sends to `203.0.113.90` and the pattern exists since day one | Flow profile, logons, processes, `vs-cambios`. Open: what `198.18.50.5` is, and no ticket covers the server |
| `10.10.12.43` | **MX-LT-0043**, laptop of contractor `mkt-contractor-04` with a normal profile (DC, servers, proxy; ≤ ~10 MB per destination). No agent | **No**: it *clicked* the phishing link (15:02:12 UTC) but no POST is recorded | Flow profile + proxy log (see E5) |
| `10.10.77.3`, `10.30.9.40` | Talk only to `10.10.30.12` / `10.30.30.12` on ports 445, 9100, 161 with 0.4–3 MB | **No** | *Hypothesis only* (printers/monitoring): no logons, no agent |
