# Rösti plugins

Plugin marketplace for [Rösti](https://rosti.dev) threat intelligence: check IOCs, research CVEs, read reports and YARA rules, and export indicators.

| Plugin | What it does |
| --- | --- |
| [`rosti`](plugins/rosti) | Skills for the Rösti MCP server (`https://mcp.rosti.dev/mcp`): IOC triage, CVE research, report deep dives, detection exports |

## Install in Claude Code

```
claude plugin marketplace add baderj/rosti-plugins
claude plugin install rosti@rosti-plugins
```

More about the MCP server: [rosti.dev/mcp](https://rosti.dev/mcp)

License: MIT, see `LICENSE`. Questions: rosti@bin.re
