# Builty for Claude

US building permit history and permit-derived property characteristics for about 143 million buildings, in Claude.
Characteristics cover building, roof, electrical, plumbing, water heater and HVAC ages. Data comes from the [Builty API](https://builtydata.com).

## Install (Claude Code)

```bash
claude plugin marketplace add builtyapp/claude_connector
```

```bash
claude plugin install builty@builty
```

Claude Code asks for your Builty API key when the plugin is enabled. It is stored in your OS keychain and sent as the `X-API-Key` header.
Request a key at [builtydata.com](https://builtydata.com).

## Other MCP clients

The plugin connects to the hosted MCP server `https://api.builty.app/mcp` (Streamable HTTP). Any MCP client can use it with the same header:

```bash
claude mcp add --transport http builty https://api.builty.app/mcp --header "X-API-Key: $BUILTY_API_KEY"
```

## What's included

| Part | Name | What it does |
| --- | --- | --- |
| MCP tools | `lookup_addresses` | 1–100 US addresses → properties with their permits |
| | `search_near_point` | Properties within a radius of a point |
| | `search_area` | Properties in a bounding box or polygon |
| | `get_properties` | Properties by `location_id` |
| | `suggest_addresses` | Street and city autocomplete (free) |
| | `list_property_types` | Property type labels for filters (free) |
| Skill | `builty-property-data` | Teaches Claude the tools, credit costs and fields |
| Command | `/builty:permits <address>` | One-address permit history and system ages |

All tools are read-only.

## Credits

Property searches are metered. Each returned property costs its `permit_count` in credits. `count_only: true` is free and returns the totals before you fetch.

## Example prompts

- "When was the roof at 1827 N Main St, Los Angeles, CA last replaced?"
- "List single-family homes within 1 km of 34.05, -118.25 with roofs over 20 years old."
- "Check these 50 addresses for permits since 2020" (with a pasted list).

## Support

[builtydata.com](https://builtydata.com) · [Terms](https://builtydata.com/terms)
