# 更新记录

> **工具本身永远是最新的** —— 19 个工具由服务端提供（<https://api.fundapi.cn/mcp>），
> 新增字段、新增工具**都不需要重新安装本插件**。只有插件的**接入方式**变化时才会发新版本。

---

## 2026-11-18 · 0.1.0

- 首个版本：把基金数据 API 的 MCP 端点挂进 DeepSeek Harness
- 19 个基金工具，工具名形如 `mcp__fundapi__fund_nav`
- 3 个免费工具（`fund_search` / `fund_profile` / `fund_screener_options`）无需 Key
- 配置：`apiKey`（或环境变量 `FUND_KEY`）、`url`、`serverName`、`headers`
