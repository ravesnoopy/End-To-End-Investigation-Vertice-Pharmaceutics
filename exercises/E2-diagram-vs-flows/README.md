# E2 · Network diagram vs. what actually happened
**Sources:** `vs-diagrama` (allowed flows), `vs-network` (observed flows)

| Q | Finding |
|---|---|
| 2.1 | **JMP-BR-01** (`10.20.50.11`) is the only jump host with internet egress, against the diagram. **145 flows to 2 external destinations**: `203.0.113.90` (72 flows, 38.2 GB) and `198.18.50.5` (73 flows, 14.6 MB). `JMP-MX-01` has none. |
| 2.2 | **1 flow** from the VPN pool (`10.99.0.0/22`) to something other than `JMP-MX-01`: `10.99.0.17 → 10.20.50.11:3389`, 2 Feb 02:58:30 UTC. It precedes the first RDP logon (02:58:41) by 11 seconds. Why the other six RDP logons have no flow is **not determinable** from these data. |
| 2.3 | `JMP-BR-01` sent **38,200,000,000 bytes (38.2 GB)** to `203.0.113.90` in **72 flows**: 14.0 GB (6 Feb), 12.7 GB (7 Feb), 11.5 GB (8 Feb), 24 flows per night. `bytes_in` is only 17.9 MB, so the traffic is almost one-way outbound. |
| 2.4 | The internal destination receiving most bytes (`10.10.30.5`, ~60 GB/night) is **not a finding**: (1) the diagram allows MX servers → backup over 445; sources are `10.10.30.20` (SQL-MX-01), `10.10.31.11` (APP-MX-01) and `10.10.30.12`, never JMP-BR-01. (2) The pattern is constant (5 flows, ~60 GB nightly since 19 Jan, no variation on 6–8 Feb) and internal, while the theft went to an external IP in three nights of different volume. *Limit:* `10.10.30.5` and `10.10.30.12` are not in the inventory. |
| 2.5 | Proposed rule: **source** jump hosts (`10.10.50.11`, `10.20.50.11`) → **destination** any non-private IP → **DENY + log**. Exception only with an approved change ticket stating host, destination, port and time window, with an expiry. All 72 flows were logged as `allow`. |
