# Rösti Threat Intel

Use [Rösti](https://rosti.dev) threat intelligence in Claude. Rösti collects public threat-intelligence reports from security vendors, researchers and CERTs and extracts their indicators of compromise (IOCs), YARA rules, MITRE ATT&CK techniques and CVEs. This plugin connects Claude to the free Rösti MCP server and adds four skills that tell Claude how to use it well.

## Skills

| Skill | Use it to |
| --- | --- |
| `ioc-triage` | Check IPs, domains, URLs, hashes or e-mail addresses against all reports, with false-positive context |
| `cve-research` | Find the reports about a CVE, ATT&CK technique, malware or actor and summarize what they say |
| `report-deep-dive` | Read one report: techniques, CVEs, IOCs, YARA rules and related reports |
| `detection-export` | Turn indicators into a blocklist or download: DNS and firewall feeds, Microsoft Defender, CSV, STIX 2.1, MISP, YARA |

Just describe what you need, for example "Check these indicators against Rösti" or "What do Rösti reports say about CVE-2024-3400?". Claude picks the matching skill.

## Install

In Claude Code:

```
/plugin install rosti --marketplace baderj/rosti-plugins
```

In Claude chat and Cowork, add it from **Customize > Plugins** once it is listed in the directory.

## Connector

`.mcp.json` points at the remote MCP server `https://mcp.rosti.dev/mcp`. All 11 tools are read-only. In Claude Code the server connects when the plugin loads. In Claude chat and Cowork, connect it once from the plugin's **Connectors** tab.

No account is needed. Anonymous users share one quota. When it is used up, Claude tells you and you can sign in with a free Rösti account (OAuth, nothing to configure) to get your own quota. Claude Code users can instead send their own API key in the `X-API-Key` header; see [rosti.dev/mcp](https://rosti.dev/mcp).

## Data

The plugin has no code of its own and stores nothing. Whatever Claude looks up (IOC values, report ids, search terms) is sent to `mcp.rosti.dev`, which forwards it to the Rösti API and returns the results. IOC lookups also send the indicator values to the Rösti False Positive Warninglist at `https://fpbuster.rosti.dev/api/v1` to check for likely false positives, such as popular domains and public DNS resolvers. The server keeps access logs for 90 days; see the [privacy policy](https://rosti.dev/privacy#mcp). Do not look up values you are not allowed to share with a third party.

## Support

Questions or problems: rosti@bin.re

## License

MIT, see `LICENSE`.
