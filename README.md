# Recent Funding Rounds Feed MCP Server

[![Smithery](https://smithery.ai/badge/mambabuilt/mcp-recent-funding-rounds-feed)](https://smithery.ai/servers/mambabuilt/mcp-recent-funding-rounds-feed) [![Glama score](https://glama.ai/mcp/servers/mambalabsdev/mcp-recent-funding-rounds-feed/badges/score.svg)](https://glama.ai/mcp/servers/mambalabsdev/mcp-recent-funding-rounds-feed) [![MCP Registry](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fregistry.modelcontextprotocol.io%2Fv0%2Fservers%3Fsearch%3Dcom.mambabuilt%252Fmcp-recent-funding-rounds-feed%26limit%3D1&query=%24.servers%5B0%5D._meta%5B%22io.modelcontextprotocol.registry%2Fofficial%22%5D.status&label=mcp%20registry&color=blue)](https://registry.modelcontextprotocol.io/v0/servers?search=com.mambabuilt/mcp-recent-funding-rounds-feed&limit=1) [![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-recent-funding-rounds-feed)](https://www.npmjs.com/package/@mambalabsdev/mcp-recent-funding-rounds-feed) [![npm downloads](https://img.shields.io/npm/dm/@mambalabsdev/mcp-recent-funding-rounds-feed)](https://www.npmjs.com/package/@mambalabsdev/mcp-recent-funding-rounds-feed) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-recent-funding-rounds-feed)](https://github.com/mambalabsdev/mcp-recent-funding-rounds-feed/blob/main/LICENSE) [![mcpservers.org](https://img.shields.io/badge/mcpservers.org-listed-blue)](https://mcpservers.org/servers/mambalabsdev/mcp-recent-funding-rounds-feed)

An MCP server for the Mamba Labs Recent Funding Rounds Feed actor on Apify. Give it a filing window and filters such as state, industry, and minimum size. Each run reads SEC Form D notices from SEC EDGAR and returns one row per new private funding round.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Your keys](#your-keys)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

The actor reads the Form D notices US companies file with the SEC within 15 days of a private funding round, keeps the ones in your window that match your filters, and returns one flat row per round: company, amount sold and offered, what was sold (equity, debt, a SAFE), investor count, industry group, executives and directors with roles, address and phone from the filing, the SEC links, and the company domain when the company's own homepage proves it. Funds, SPVs, and real estate projects are dropped by default. When nothing matches, one free row says why.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer and an Apify account with an API token.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-recent-funding-rounds-feed": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-recent-funding-rounds-feed"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token"
      }
    }
  }
}
```

Restart the client. The `find_recent_funding_rounds` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.

## Example prompts

- "Which tech and biotech companies filed a Form D for a round over 1 million USD this week?"
- "List companies in New York and Massachusetts that raised money in the last 30 days, with their domains."
- "Find newly funded AI startups from SEC filings this week and give me their executives."

## Tool

`find_recent_funding_rounds`. One tool. Example arguments:

```json
{
  "filed_within_days": "7",
  "industry_groups": [
    "Other Technology"
  ],
  "min_amount_usd": "500000"
}
```

## Inputs

Every buyer facing input of the actor, unchanged. Omitted inputs take the actor's own defaults. Numbers and booleans are sent as strings.

| Input | Type | Description |
|---|---|---|
| `filed_within_days` | string | Filing window in days ending today, 1 to 90, today included. Ignored when date_from is set. Default 7. |
| `date_from` | string | Start of a custom filing window, YYYY-MM-DD. The window is at most 92 days. |
| `date_to` | string | End of a custom filing window, YYYY-MM-DD. Default today. |
| `states` | array | Two character state codes of the company's principal place of business, such as CA, NY, or TX. |
| `industry_groups` | array | Keep only these Form D industry groups, such as Other Technology, Computers, Biotechnology, Pharmaceuticals, Business Services, Retailing, Manufacturing. |
| `exclude_industry_groups` | array | Drop these Form D industry groups. The actor default drops Pooled Investment Fund, Commercial, Residential, REITS and Finance, and Other Real Estate; send an empty list to keep them. |
| `exclude_spvs` | string | Drop SPVs and funds (3(c) exemptions, pooled fund interests, SPV or Fund in the name). Default true. |
| `min_amount_usd` | string | Keep a round only when the larger of amount offered and amount sold is at least this many USD. Default 100000; 0 keeps every size. |
| `name_keywords` | array | Words in the company name, such as AI or Bio. |
| `include_amendments` | string | Also return Form D/A amendments. Default false. |
| `resolve_domains` | string | Find each company's domain and keep it only when its homepage names the company. No extra charge. Default true. |
| `include_press` | string | Add a matching funding headline for the 50 largest rounds through the Mamba Labs News and Media Mention Monitor, which bills its own article events (up to 100 a run) to your Apify account. Default false. |
| `max_rounds` | string | Round rows per run, newest filing first, up to 2000. Each round row is billed. Default 100. |
| `only_new` | string | Skip rounds an earlier run with the same state_key returned. Default false. |
| `state_key` | string | Names the seen list for only_new. |
| `max_charge_usd` | string | Optional. The run stops before any row that would take its charges over this amount. |

## Pricing

Pay per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `round-row`, per funding round row | $0.01 | $0.0095 | $0.009 | $0.0085 |
| `apify-actor-start`, per run start (one per GB of run memory, minimum one; the default is 512 MB) | $0.0015 | $0.0015 | $0.0015 | $0.0015 |

The no results row, a refused input row, and rounds skipped by `only_new` are free. With `include_press` set to `true`, the actor starts one run of the Mamba Labs News and Media Mention Monitor on your account, which bills its own article events.

## Your keys

None beyond your Apify token. SEC EDGAR is free and needs no key. Mamba Labs pays the platform and proxy costs of the run.

## Full actor documentation

[apify.com/mambalabs/recent-funding-rounds-feed](https://apify.com/mambalabs/recent-funding-rounds-feed)

## License

MIT. See [LICENSE](LICENSE).
