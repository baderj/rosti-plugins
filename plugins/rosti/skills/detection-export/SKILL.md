---
name: detection-export
description: Turn Rösti report data into something a defender can deploy or import, such as a blocklist, hunting query input, YARA rule file, or a CSV, STIX or MISP export. Use when the user wants indicators or rules from a report or topic for a SIEM, firewall, EDR, MISP or threat-intelligence platform.
---

# Detection and export

Build deployable output from Rösti data, with the false-positive caveats the user needs.

## Steps

1. Identify the source: a report id, or a topic (then find reports with `search_reports`, or indicators directly with `search_iocs` using `types`, `tags`, `categories` and `since`).
2. Fetch indicators with `list_report_iocs` or `search_iocs`. Call `describe_taxonomy` once to get the valid type, tag and category values and the false-positive risk scale. Page through all results when the user wants a complete list.
3. Filter before output:
   - keep only types the target system handles (for example `ip-dst`, `domain` and `url` for a proxy; hashes for an EDR)
   - keep indicators flagged for IDS when the target is a network detection system
   - drop or separately list indicators with a false-positive risk above "no indication" and anything that a lookup shows as probably benign; popular domains and public resolvers cause outages when blocked
4. For rules, call `get_yara_rules` and output the rules unchanged in one file.
5. For a complete file, call `get_export_link` with the report id and a format id from `get_report` (for example `csv` or `misp`). The link is signed and expires after 15 minutes; give it to the user to open and do not try to download it.

## Output

Offer the format the target needs: one value per line for blocklists, CSV with type, value, tags and report for tickets, or a query for the user's tool when they name it. State how many indicators were included, how many were left out and why, and the report each came from. Remind the user that a match means the value was mentioned in a report, so review before blocking.
