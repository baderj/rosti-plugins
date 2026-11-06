---
name: detection-export
description: Turn Rösti report data into something a defender can deploy or import, such as a DNS or firewall blocklist, an EDR indicator file, a YARA rule file, or a CSV, STIX 2.1 or MISP export. Use when the user wants indicators or rules from a report or topic for a SIEM, firewall, DNS resolver, proxy, EDR, MISP or threat-intelligence platform.
---

# Detection and export

Build deployable output from Rösti data, with the false-positive caveats the user needs.

## Prefer a ready-made export

Rösti exports every report in many formats. When the user wants the indicators of one report for a specific system, give them the matching export instead of building the file by hand:

| Target | Format ids |
| --- | --- |
| Threat-intelligence platforms | `misp`, `stix2` (STIX 2.1), `json`, `jsonl`, `csv` |
| SIEM | `ecs` (Elastic Common Schema), `csv`, `generic-csv`, `generic-jsonl` |
| DNS resolvers and blocklists | `domains`, `hosts`, `hosts-compressed`, `rpz`, `dnsmasq`, `wildcard-domains`, `wildcard-asterisks` |
| Browsers and proxies | `adblock`, `pac` |
| Microsoft Defender for Endpoint | `mde-all`, `mde-sha1`, `mde-sha256`, `mde-ip`, `mde-domain`, `mde-url` |
| Firewalls | `fortinet-ips`, `fortinet-domains`, `fortinet-hashes`, `checkpoint-*`, `cortex-json`, `cortex-csv` |
| Detection rules | `yara`, `yara-zip` |

`get_report` lists the formats available for a report; `describe_taxonomy` lists all format ids. Call `get_export_link` with the report id and the format id. The link is signed and expires after 15 minutes: give it to the user to open, and do not try to download it.

## Build a custom list

When no export fits, the user wants indicators from several reports, or the list needs filtering:

1. Find the indicators with `list_report_iocs` (one report) or `search_iocs` (across reports, with `types`, `tags`, `categories` and `since`). Call `describe_taxonomy` once for the valid values. Page through all results with `next_cursor` when the user wants a complete list.
2. Keep only types the target handles, for example `ip`, `cidr`, `domain` and `url` for a firewall or proxy, and `md5`, `sha1` and `sha256` for an EDR. Split combined types such as `ip:port`, `domain:port` and `domain:ip` when the target needs plain values.
3. Keep indicators flagged for IDS when the target is a network detection system.
4. Leave out, or list separately with the reason:
   - indicators with a false-positive risk of medium, high or very high (the levels are informational, small, moderate, medium, high, very high)
   - indicators tagged `lookup`, `tracker` or `lol`, which are usually legitimate services
   - anything `lookup_iocs` marks with a `false_positive_verdict`
   Blocking popular domains, public resolvers or geolocation services breaks legitimate traffic.
5. For rules, call `get_yara_rules` and output the rules unchanged in one file.

## Output

Give the format the target needs: one value per line for blocklists, CSV with type, value, tags and report for tickets, or a query for the user's tool when they name it. State how many indicators were included, how many were left out and why, and the report each came from. Remind the user that a match means the value was mentioned in a report, so review before blocking.
