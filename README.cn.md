<div align="center">

```
███████╗██╗   ██╗ █████╗ ███╗   ██╗██╗     ██╗ ██████╗ █████╗ ████████╗
██╔════╝██║   ██║██╔══██╗████╗  ██║██║     ██║██╔════╝██╔══██╗╚══██╔══╝
███████╗██║   ██║███████║██╔██╗ ██║██║     ██║██║     ███████║   ██║
╚════██║██║   ██║██╔══██║██║╚██╗██║██║     ██║██║     ██╔══██║   ██║
███████║╚██████╔╝██║  ██║██║ ╚████║███████╗██║╚██████╗██║  ██║   ██║
╚══════╝ ╚═════╝ ╚═╝  ╚═╝╚═╝  ╚═══╝╚══════╝╚═╝ ╚═════╝╚═╝  ╚═╝   ╚═╝
```

# suanlicat-bulk-apikey

**批量创建 SuanliCat 账户并自动生成 API 密钥 — 兼容 OpenAI 的推理 + 9Router 连接器。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 快速开始](#-quick-start) · [🎁 免费额度](#-free-tier--how-much-you-get) · [🤖 模型](#-models--all-17-of-them) · [🧪 使用 API](#-how-to-use-the-api) · [✅ 已验证](#-verified-working--every-key-is-tested) · [🧭 工作原理](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ 选项](#️-cli-options) · [🛰️ 代理](#️-behind-a-proxy) · [🐞 问题反馈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ 为什么有这个项目

SuanliCat（算力狐）为**每个新账户赠送 100,000 额度单位（按 qpu=100,000 计算约合 $1）**，并额外提供**每日签到约 13,270 单位**。注册只需邮箱 + 6 位验证码，无需验证码；当前可用的密钥线路为 `deepseek-v4.1-flash`，整个工具已通过端到端验证——成功铸造 **100 个账户 → 100 个已验证密钥**。

## 🎁 免费额度 — 你能获得多少

| 项目 | 数值 |
| --- | --- |
| 注册赠送额度（`quotaStart`） | **100,000 单位 ≈ $1**（qpu=100,000） |
| 每日签到 | **约 +13,270 单位/天**（实测；CLI 在保留密钥前会为每个账户签到一次） |
| 注册 | 邮箱 + 6 位验证码 — **无需验证码**（`turnstile_check: false`） |
| 模型目录 | 实时价格表上有 17 个模型条目 |
| 速率限制 | 突发请求下注册接口返回 `429`（`register refused`）；CLI 会退避约 25 秒并使用同一个新验证码重试 |
| 可扩展的单位 | **一个账户 = 一个密钥** |

> 💡 免费额度由 SuanliCat（算力狐）设定，可能发生变化。请前往 [suanlicat.top](https://suanlicat.top) 查看实时数据——并让工具自身的校验来决定：

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 模型 — 全部 17 个

实时价格表包含 **17 个模型条目**（`qwen-turbo`、`doubao-code`、`doubao-pro-max`、`coze-*`、`deepseek-*` …）。已验证的密钥线路：

| 模型 ID | 已验证 |
| --- | --- |
| `deepseek-v4.1-flash` | 铸造期间在 `providers/suanlicat/keys.txt` 有 **100 个密钥 chat-200**；通过随附的 CLI 独立复检（见已验证表格） |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 如何使用 API

基础 URL **`https://suanlicat.top/v1`** — 兼容 OpenAI。

```python
from openai import OpenAI
c = OpenAI(base_url="https://suanlicat.top/v1", api_key="beoLYs…")
print(c.chat.completions.create(model="deepseek-v4.1-flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 快速开始

```bash
cd suanlicat-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

只有当 `/v1/chat/completions` 返回 200 且包含真实内容后，密钥才会被写入 `keys.txt`；`accounts.json` 保存用户名 / 邮箱 / quotaStart → quotaAfterCheckin / group。

### 🖱️ 启动器

启动器会询问代理列表和数量，记住它们，并运行 CLI——铸造、验证、出口检测、设置、帮助、连接到 9Router。

## ✅ 已验证可用 — 每个密钥都经过测试

| 检查项 | 结果 |
| --- | --- |
| `GET /api/status` | **200** — 注册开启、邮箱验证开启、验证码关闭、签到开启 |
| `POST /api/user/checkin` | **200** — 每日奖励到账 |
| `POST /api/token/`（+ 显示） | **200** — 密钥材料仅返回一次 |
| `POST /v1/chat/completions`（`deepseek-v4.1-flash`）— 对**每个**密钥在写入前执行 | **200** 且包含真实内容 — 100 个密钥的铸造以 100/100 完成 |
| 独立复检 — 通过随附的 CLI **100/100 个密钥可用**（`--verify-only`，全新退出，`deepseek-v4.1-flash` chat-200，2026-10-20） |
| 面板侧说明 | 注册在突发请求下会以 `429` 限流 — CLI 等待约 25 秒后重试；邮件始终未到达（共享临时邮箱域名）时会切换到下一个邮箱 |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 工作原理

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: SuanliCat (算力狐) page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://suanlicat.top/v1/chat/completions → real content = the key is live
```

| 步骤 | 调用 | 说明 |
| --- | --- | --- |
| status | `GET /api/status` | 注册/邮箱/签到开关（验证码关闭） |
| mailbox | Emailnator `POST /api/generate-email` | 每个账户一个全新的 @gmail.com（无点形式） |
| send code | `GET /api/verification?email=…` | 6 位验证码 |
| register | `POST /api/user/register` | 429 → 等待，使用同一个新验证码重试 |
| login | `POST /api/user/login` | 会话令牌 |
| check-in | `POST /api/user/checkin` | 每日奖励 |
| key | `POST /api/token/`（+ 显示） | 仅写入一次 |
| proof | `POST /v1/chat/completions` | 200 且包含内容 = 密钥可用 |

## ⚙️ CLI 选项

```bash
node src/cli.js --count N        # mint N keys (check-in + chat gate per account)
node src/cli.js --group default
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| 环境变量 | 默认值 | 含义 |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | 出口池，每行一个代理 |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | 用于实时验证的模型 |

## 🛰️ 在代理后面

**可选 — 大规模使用时推荐（按 IP 限制、封禁）。** 该工具在没有代理的情况下也能工作。

- **有代理池时：** 传入 `--pool <file>`（或设置 `SUANLICAT_PROXIES`），每个请求都会经过该列表中的一个出口——失效线路会使该次尝试失败，工具会切换到下一个（提供代理池时采用故障关闭策略；代理池在使用时会重新探测）。
- **无代理池时：** CLI 会打印 `running WITHOUT a proxy (direct)` 并直接连接。注册、签到、密钥铸造或验证都不会改变——只是数据包的出口不同。
- 对任一模式运行 `--screen`：它会在真实运行前针对提供商测试你拥有的出口（或直连路由）。

## 📄 文件

| 路径 | 内容 |
| --- | --- |
| `keys.txt` | 每行一个实时验证的密钥 — 已被 git 忽略 |
| `accounts.json` | 用户名 / 邮箱 / quotaStart / quotaAfterCheckin / group |
| `proxies.txt` | 你的出口 — 已被 git 忽略 |
| `outputs/` | 运行日志 — 已被 git 忽略 |

## 🔌 连接到 9Router

[9Router](https://9router.com) 是一个自托管、兼容 OpenAI 的路由器：

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

默认值：`--prefix suanlicat`、`--base-url https://suanlicat.top/v1`、`--model deepseek-v4.1-flash`。模型以 `suanlicat/<model id>` 形式提供。

## 🔑 一个密钥能给你什么

| | |
| --- | --- |
| 🏢 提供商 | **SuanliCat（算力狐）** — [suanlicat.top](https://suanlicat.top) |
| 🎁 提供商赠送 | 注册赠送 $1（100,000 单位）+ 每日签到约 13k |
| 🔑 一个密钥能给你什么 | 一个独立账户上的一个密钥，已在 `deepseek-v4.1-flash` 上通过聊天验证 |
| 🌐 API 形式 | 兼容 OpenAI：`POST https://suanlicat.top/v1/chat/completions` |
| 🔒 你的数据 | 仅联系 `suanlicat.top` 和你的邮箱后端 |

## 🐞 问题反馈

如果 SuanliCat 更改了其注册开关或签到奖励，工具会在此处停止并说明原因。请**[提交 issue](../../issues)**，附上命令和确切输出。

## 📄 许可证

MIT © 2026 — 见 [LICENSE](LICENSE)。

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey contributors

</div>