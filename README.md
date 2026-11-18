<div align="center">

<a href="https://fundapi.cn"><img src="https://fundapi.cn/icon-512.png" width="88" height="88" alt="基金数据 API"></a>

# fundapi — 基金数据 API 官方 DeepSeek Harness 插件

让 **DeepSeek Harness（DSH）** 直接调用基金数据：净值 / 持仓 / 调仓变化 / 基金经理 / 评级 / 排行 / 选基 / 组合穿透 / 分红 / 交易规则 —— **19 个工具，3 个免费工具无需 Key**。

桌面端、Web UI、命令行都能装 —— [下载桌面端](https://www.deepseek.com/harness/) 后，在「插件」页里输个包名就行。

![Node.js](https://img.shields.io/badge/node-%E2%89%A520-339933?logo=nodedotjs&logoColor=white)
![DeepSeek Harness](https://img.shields.io/badge/DeepSeek%20Harness-plugin-4D6BFE)
[![npm](https://img.shields.io/npm/v/fundapi-dsh-plugin?label=npm)](https://www.npmjs.com/package/fundapi-dsh-plugin)
![License](https://img.shields.io/badge/license-MIT-green)

**支持 DeepSeek Harness 桌面端与 Web UI**：

![DeepSeek Harness 桌面端](https://img.shields.io/badge/DeepSeek%20Harness-Desktop-4D6BFE?logo=deepseek&logoColor=white)
![DeepSeek Harness Web UI](https://img.shields.io/badge/DeepSeek%20Harness-Web%20UI-4D6BFE?logo=deepseek&logoColor=white)

**中文** · [English](README.en.md)

[官网](https://fundapi.cn) · [文档](https://fundapi.cn/docs/) · [MCP 接入](https://fundapi.cn/mcp/) · [价格](https://fundapi.cn/pricing/) · [常见问题](https://fundapi.cn/faq/)

[GitHub 源码](https://github.com/fundapi-cn/fundapi-dsh-plugin) · [问题反馈 Issues](https://github.com/fundapi-cn/fundapi-dsh-plugin/issues) · [更新日志](CHANGELOG.md)

</div>

---

## 这是什么

一个 **基金数据 DeepSeek Harness 插件**：装上之后，你在 DSH 里问「110022 这只基金最近涨得怎么样」，它会自己去调工具拿**真实数据**，而不是编一个数给你。

桌面端、Web UI、命令行都能用 —— 桌面端 / Web UI 在「插件」页面输一个包名；命令行一条命令。

底层接的是 [fundapi.cn](https://fundapi.cn) 的 **MCP 服务**（一个 URL）。插件本身只负责把这个服务挂进 DSH —— 传输、工具注册、断线重连都由 DSH 自带的 MCP 客户端处理，所以 DSH 升级时插件基本不用跟着改。

---

## 为什么用它

- **桌面端 / Web UI 输个包名就装好**：不用配 JSON、不用写脚本，装完重启即可用
- **免费工具真的免 Key**：`fund_search` / `fund_profile` / `fund_screener_options` 这 3 个装上就能调，不用先买 Key
- **工具描述写给 AI Agent 看**：每个工具都写清「什么时候用 / 参数怎么填 / 返回什么」，以及**容易读错的地方**——比如「新进前十」不等于「新买入」、「估算涨跌」不等于「净值」
- **估算与净值分得开**：当日净值公布前给的是**估算值**，工具会标明这是估算，不会把估算说成净值
- **只并列、不给结论**：基金对比工具把 2–5 只基金的收益、费率、经理、重仓并排列出，不生成综合评分，也不做推荐
- **薄**：插件不含任何数据逻辑，只有一份配置 —— 数据与计算都在服务端，出问题最好排查

---

## 安装

### 桌面端 / Web UI（推荐）

[下载 DeepSeek Harness 桌面端](https://www.deepseek.com/harness/)（macOS / Windows）后，打开左侧「**插件**」页面，输入包名：

```
fundapi-dsh-plugin
```

Web UI（`dsh web`）里是同一个「插件」页面，操作一样。装完直接就能在对话里用 —— 不用命令行、不用改配置文件。

### 命令行

```bash
npx @deepseek-ai/dsh web                                    # 启动 Web UI
npx @deepseek-ai/dsh plugin --profile web add fundapi-dsh-plugin
```

（已全局装过 `dsh` 的话，把 `npx @deepseek-ai/dsh` 换成 `dsh` 即可。）

> 命令行安装和 Web UI 的「插件」页面都会调用本机的 **pnpm**（DSH 不自带，桌面端自带一份）—— 没装的话先 `npm i -g pnpm`。

装完**重启 `dsh web`** 生效。启动前也可以先确认这一层挂上了：

```bash
npx @deepseek-ai/dsh --profile web --dump-config   # 输出里应出现 # == fundapi-dsh-plugin
```

> ⚠️ 桌面端的 profile 由桌面应用自己管理，**不能用命令行装** —— 桌面端请走上面的「插件」页面。

然后在对话里直接问：

| 你可以这样问 | 会用到的工具 |
|---|---|
| 易方达消费行业是哪个代码？ | `fund_search` |
| 110022 费率多少、规模多大、谁在管？ | `fund_profile` |
| 这只基金最近一年涨了多少？ | `fund_nav` |
| 这只基金重仓了哪些股票？ | `fund_holdings` |
| 这季新进了什么、减了什么？ | `fund_holding_changes` |
| 我这几只基金是不是买了一样的股票？ | `fund_overlap` |
| 我这三只基金合起来实际押在哪些股票上？ | `fund_portfolio_xray` |
| 帮我按近三年收益 + 晨星五星筛一下股票型 | `fund_screener` |
| 这只基金今天大概涨跌多少？ | `fund_estimate` |
| 这几只基金到底差在哪？ | `fund_compare` |

其他安装方式：

```bash
# 从 GitHub
npx @deepseek-ai/dsh plugin --profile web add github:fundapi-cn/fundapi-dsh-plugin

# 从本地目录（开发调试）
npx @deepseek-ai/dsh plugin --profile web add file:/绝对路径/fundapi-dsh-plugin
```

> 本插件是纯 JavaScript、**没有构建步骤**，所以从 GitHub 装也是装完即用 —— 不需要给 pnpm 开 `allowBuilds` 授权。

卸载：

```bash
npx @deepseek-ai/dsh plugin --profile web remove fundapi-dsh-plugin
```

---

## 配置 API Key

上面 3 个免费工具**不用配任何东西**就能用。

要用其余 16 个工具（净值 / 持仓 / 调仓 / 经理 / 评级 / 排行 / 选基 / 组合穿透 / 分红 / 交易规则…），需要一个 Key：[获取 Key](https://fundapi.cn/pricing/)（体验版 ¥9.9 / 7 天，专业版 ¥59/月，机构版 ¥299/月）。

**方式一：环境变量**（最省事）

在 `~/.dsh/.env` 里加一行（`$DSH_HOME` 改过就放那儿）：

```
FUND_KEY=cf-你的KEY
```

**方式二：写进 profile**

编辑对应 profile 的 `cordis.patch.yml` —— Web UI / 命令行是 `~/.dsh/profiles/web/cordis.patch.yml`，桌面端是 `~/.dsh/profiles/desktop/cordis.patch.yml`（桌面端的目录要先打开一次桌面端才会生成）。加一条按 id 覆盖的补丁：

```yaml
- id: mcp-fundapi
  name: fundapi-dsh-plugin
  config:
    apiKey: cf-你的KEY
```

> ⚠️ 这条补丁会**整块替换**该条目的 `config`，所以只写你要改的字段即可 —— 其余字段（端点、命名空间）用插件内置默认值。

**可配置项**

| 字段 | 默认值 | 说明 |
|---|---|---|
| `apiKey` | 读环境变量 `FUND_KEY` | 不填则走匿名，只有 3 个免费工具可用 |
| `url` | `https://api.fundapi.cn/mcp` | 走自建代理时改这里 |
| `serverName` | `fundapi` | 工具名前缀，即 `mcp__fundapi__*`；`[A-Za-z0-9_-]{1,32}` |
| `headers` | `{}` | 额外请求头，与上面的 Key 合并 |

其余字段（`toolCallTimeoutMs`、`failOnStartupError` 等）交给 DSH 自带的 MCP 客户端。

---

## 工具

装好后工具名形如 `mcp__fundapi__fund_nav`。

**免费（无需 Key）**

`fund_search` 基金搜索 · `fund_profile` 基金档案（费率 / 阶段涨幅 / 经理 / 规模 / 仓位 / 持有人结构 / 前十大） · `fund_screener_options` 筛选项字典

**体验版及以上（¥9.9 / 7 天）**

`fund_nav` 历史净值 · `fund_rank` 全市场排行 · `fund_notices` 公告

**专业版及以上（¥59/月）**

`fund_holdings` 季报持仓 · `fund_managers` 现任经理 · `fund_estimate` 当日估算 · `fund_dividends` 分红与折算 · `fund_trade_rule` 交易规则与费率 · `fund_rating` 多家机构评级

**机构版（¥299/月）**

`fund_overlap` 持仓重叠 · `fund_portfolio_xray` 组合穿透 · `fund_holding_changes` 调仓变化 · `fund_manager_history` 经理历任 · `fund_portfolio_diagnose` 组合诊断 · `fund_screener` 多条件选基 · `fund_compare` 基金对比

字段含义、参数与返回样例见[文档](https://fundapi.cn/docs/)。

---

## 开发

```bash
npm test   # 配置组装 + 拼写提示的回归护栏
```

插件本体只有 `index.js` 一个文件、**零运行时依赖**：它把配置交给 DSH 自带的 MCP 客户端，不重复实现传输逻辑。这样 DSH 升级时通常不需要跟着改。

---

## 相关

**MCP 服务器**：`https://api.fundapi.cn/mcp`（[配置说明](https://fundapi.cn/mcp/)）—— 不用 DSH？Claude Code / Codex / Cursor 等客户端填一条 URL 就能接

**自托管**：仓库里不含数据逻辑；单文件 MCP 脚本可从 <https://fundapi.cn/fund-mcp.py> 下载

---

## 版本

最新版本见 [CHANGELOG.md](CHANGELOG.md)。

---

## License

MIT
