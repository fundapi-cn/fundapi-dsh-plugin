<div align="center">

<a href="https://fundapi.cn"><img src="https://fundapi.cn/icon-round.png" width="88" height="88" alt="Fund Data API"></a>

# fundapi — Official DeepSeek Harness Plugin for the Fund Data API

Give **DeepSeek Harness (DSH)** direct access to China fund data: NAV history, holdings, position changes, fund managers, ratings, rankings, screening, portfolio look-through, dividends and trading rules — **19 tools, 3 of them free and key-less**.

Install it from the desktop app, the Web UI or the CLI — [download the desktop app](https://www.deepseek.com/harness/) and type the package name on the **Plugins** page.

![Node.js](https://img.shields.io/badge/node-%E2%89%A520-339933?logo=nodedotjs&logoColor=white)
![DeepSeek Harness](https://img.shields.io/badge/DeepSeek%20Harness-plugin-4D6BFE)
[![npm](https://img.shields.io/npm/v/fundapi-dsh-plugin?label=npm)](https://www.npmjs.com/package/fundapi-dsh-plugin)
![License](https://img.shields.io/badge/license-MIT-green)

**English** · [中文](README.md)

[Website](https://fundapi.cn) · [Docs](https://fundapi.cn/en/docs/) · [MCP setup](https://fundapi.cn/en/mcp/) · [Pricing](https://fundapi.cn/en/pricing/) · [FAQ](https://fundapi.cn/en/faq/)

[Source](https://github.com/fundapi-cn/fundapi-dsh-plugin) · [Issues](https://github.com/fundapi-cn/fundapi-dsh-plugin/issues) · [Changelog](CHANGELOG.md)

</div>

---

## What it is

A **DeepSeek Harness plugin for fund data**. Once installed, asking DSH "how has fund 110022 performed lately?" makes it call a tool and answer with **real data** instead of inventing numbers.

The plugin does one thing: it connects DSH to the **MCP endpoint** at [fundapi.cn](https://fundapi.cn). Transports, tool registration and reconnects are handled by the MCP client that ships with DSH, so most DSH upgrades need no change here.

---

## Why use it

- **Install by package name** in the desktop app or Web UI — no JSON, no scripts; restart and it works
- **The free tools need no key**: `fund_search`, `fund_profile` and `fund_screener_options` work right away
- **Labels are spelled out**: "newly in the top ten" is not "newly bought", and an intraday estimate is not the NAV — the tool descriptions say so
- **Aligned side by side, differences at a glance**: key metrics for 2–5 funds on one comparable basis
- **No data is not a failure**: an agency that did not rate a fund in a period, or a bond fund with no equity to estimate, comes back with a stated reason instead of an error
- **Tool descriptions are written for AI agents**: when to use each tool, how to fill the parameters, what comes back
- **Nothing for you to maintain**: data and computation live on the server
- **Thin by design**: the plugin holds configuration only

---

## Install

### Desktop app / Web UI (recommended)

[Download DeepSeek Harness](https://www.deepseek.com/harness/) (macOS / Windows), open **Plugins** in the sidebar and enter:

```
fundapi-dsh-plugin
```

The Web UI (`dsh web`) has the same Plugins page. Restart after installing.

### CLI

```bash
npx @deepseek-ai/dsh web                                    # start the Web UI
npx @deepseek-ai/dsh plugin --profile web add fundapi-dsh-plugin
```

If `dsh` is installed globally, drop the `npx @deepseek-ai/dsh` prefix.

> Both the CLI and the Plugins page use the local **pnpm** (DSH does not bundle it; the desktop app ships its own) — run `npm i -g pnpm` if you don't have it.

Verify before starting:

```bash
npx @deepseek-ai/dsh --profile web --dump-config   # should contain # == fundapi-dsh-plugin
```

> ⚠️ The desktop app manages its own profile and **cannot** be configured from the CLI — use the Plugins page there.

Then just ask:

| You ask | Tool used |
|---|---|
| Which fund code is "E Fund Consumer Sector"? | `fund_search` |
| What are the fees, size and manager of 110022? | `fund_profile` |
| How did this fund perform over the past year? | `fund_nav` |
| Which stocks does this fund hold? | `fund_holdings` |
| What was added or trimmed last quarter? | `fund_holding_changes` |
| Do my funds hold the same stocks? | `fund_overlap` |
| What does my three-fund portfolio actually hold? | `fund_portfolio_xray` |
| Screen equity funds by 3-year return and a 5-star rating | `fund_screener` |
| How much is this fund up or down today? | `fund_estimate` |
| What are the real differences between these funds? | `fund_compare` |

Other install sources:

```bash
# From GitHub
npx @deepseek-ai/dsh plugin --profile web add github:fundapi-cn/fundapi-dsh-plugin

# From a local directory (development)
npx @deepseek-ai/dsh plugin --profile web add file:/absolute/path/fundapi-dsh-plugin
```

> Pure JavaScript, **no build step** — installing from GitHub works immediately and needs no `allowBuilds` permission for pnpm.

Uninstall:

```bash
npx @deepseek-ai/dsh plugin --profile web remove fundapi-dsh-plugin
```

---

## API key

The 3 free tools need nothing. The other 16 (NAV history, holdings, position changes, managers, ratings, rankings, screening, look-through, dividends, trading rules…) need a key: [get one](https://fundapi.cn/pricing/) — three tiers: Trial, Pro and Institutional (see the pricing page).

**Option 1: environment variable**

Add to `~/.dsh/.env` (or `$DSH_HOME` if you moved it):

```
FUND_KEY=cf-your-key
```

**Option 2: profile config**

Edit `~/.dsh/profiles/web/cordis.patch.yml` (Web UI / CLI) or `~/.dsh/profiles/desktop/cordis.patch.yml` (desktop app; the directory appears after the app has been opened once):

```yaml
- id: mcp-fundapi
  name: fundapi-dsh-plugin
  config:
    apiKey: cf-your-key
```

> ⚠️ This patch **replaces** the entry's whole `config`, so list only the fields you want to change.

**Options**

| Field | Default | Notes |
|---|---|---|
| `apiKey` | reads `FUND_KEY` | Without it you run anonymously: 3 free tools only |
| `url` | `https://api.fundapi.cn/mcp` | Point at a self-hosted proxy if you have one |
| `serverName` | `fundapi` | Tool prefix, i.e. `mcp__fundapi__*`; `[A-Za-z0-9_-]{1,32}` |
| `headers` | `{}` | Extra request headers, merged with the key above |

Remaining fields (`toolCallTimeoutMs`, `failOnStartupError`, …) go to the MCP client bundled with DSH.

---

## Tools

Tool names look like `mcp__fundapi__fund_nav`.

**Free (no key)**

`fund_search` · `fund_profile` (fees, period returns, manager, size, asset mix, holder structure, top ten holdings) · `fund_screener_options`

**Trial and above**

`fund_nav` NAV history · `fund_rank` market-wide rankings · `fund_notices` announcements

**Pro and above**

`fund_holdings` quarterly holdings · `fund_managers` current managers · `fund_estimate` intraday estimate · `fund_dividends` dividends and splits · `fund_trade_rule` trading rules and fees · `fund_rating` multi-agency ratings

**Institutional**

`fund_overlap` holding overlap · `fund_portfolio_xray` portfolio look-through · `fund_holding_changes` position changes · `fund_manager_history` manager tenure history · `fund_portfolio_diagnose` portfolio diagnosis · `fund_screener` multi-criteria screening · `fund_compare` fund comparison

Field definitions and sample responses: [docs](https://fundapi.cn/docs/).

---

## Development

```bash
npm test   # regression tests for config assembly and typo hints
```

`index.js` is the whole plugin, with **zero runtime dependencies**: it hands configuration to the MCP client that ships with DSH instead of reimplementing transports.

---

## Related

**MCP server**: `https://api.fundapi.cn/mcp` ([setup](https://fundapi.cn/mcp/)) — not using DSH? Any MCP-capable client works with a single URL.

**Self-hosting**: the single-file MCP script is available at <https://fundapi.cn/fund-mcp.py>.

---

## Version

See [CHANGELOG.md](CHANGELOG.md).

---

## License

MIT
