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

**Bulk-create SuanliCat accounts with auto-generated API keys — OpenAI-compatible inference + 9Router connector.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Quick start](#-quick-start) · [🎁 Free tier](#-free-tier--how-much-you-get) · [🤖 Models](#-models--all-17-of-them) · [🧪 Use the API](#-how-to-use-the-api) · [✅ Verified](#-verified-working--every-key-is-tested) · [🧭 How it works](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Options](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Issues](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Why this exists

SuanliCat (算力狐) hands out **100,000 quota units (≈ $1 at qpu=100,000) to every new account** and pays a **daily check-in of ~13,270 units** on top. Registration is e-mail + 6-char code, captcha-free; the verified keys line is `deepseek-v4.1-flash`, and the whole tool was proven end-to-end by minting **100 accounts → 100 verified keys**.

## 🎁 Free tier — how much you get

| What | Value |
| --- | --- |
| Signup credit (`quotaStart`) | **100,000 units ≈ $1** (qpu=100,000) |
| Daily check-in | **~+13,270 units/day** (measured; the CLI checks in once per account before keeping its key) |
| Signup | e-mail + 6-char code — **no captcha** (`turnstile_check: false`) |
| Catalogue | 17 model entries on the live pricing sheet |
| Rate limits | register answers `429` under bursts (`register refused`); the CLI backs off ~25 s and retries with the same fresh code |
| The unit that scales | **one account = one key** |

> 💡 The free tier is set by SuanliCat (算力狐) and can change. Check the live numbers at [suanlicat.top](https://suanlicat.top) — and let the tool's own gate decide:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Models — all 17 of them

The live pricing sheet carries **17 model entries** (`qwen-turbo`, `doubao-code`, `doubao-pro-max`, `coze-*`, `deepseek-*` …). The verified key line:

| Model ID | Verified |
| --- | --- |
| `deepseek-v4.1-flash` | **chat-200** during the mint at `providers/suanlicat/keys.txt`; independent re-check via the shipped CLI (see the Verified table) |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 How to use the API

Base URL **`https://suanlicat.top/v1`** — OpenAI-compatible.

```python
from openai import OpenAI
c = OpenAI(base_url="https://suanlicat.top/v1", api_key="beoLYs…")
print(c.chat.completions.create(model="deepseek-v4.1-flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Quick start

```bash
cd suanlicat-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings
# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

A key is written to `keys.txt` **only** after `/v1/chat/completions` answers 200 with real content; `accounts.json` keeps username / e-mail / quotaStart → quotaAfterCheckin / group.

### 🖱️ The launcher

The launcher asks for the proxy list and count, remembers them, and runs the CLI — mint, verify, screen exits, settings, help, Connect to 9Router.

## ✅ Verified working — every key is tested

| Check | Result |
| --- | --- |
| `GET /api/status` | **200** — register on, e-mail verification on, captcha off, check-in on |
| `POST /api/user/checkin` | **200** — daily bonus lands on the account |
| `POST /api/token/` (+ reveal) | **200** — key material returned once |
| `POST /v1/chat/completions` (`deepseek-v4.1-flash`) — for **every** key before it is written | **200** with real content — the 100-key mint completed at 100/100 |
| independent re-check — **verified working** via the shipped CLI (`--verify-only`, fresh exit, `deepseek-v4.1-flash` chat-200, 2026-10-20) |
| panel-side note | register throttles with `429` under burst — the CLI waits ~25 s and retries; a mail that never arrives (shared temp-mail domains) moves to the next box |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 How it works

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: SuanliCat (算力狐) page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://suanlicat.top/v1/chat/completions → real content = the key is live
```

| Step | Call | Notes |
| --- | --- | --- |
| status | `GET /api/status` | register/e-mail/check-in gates (captcha off) |
| mailbox | Emailnator `POST /api/generate-email` | one fresh @gmail.com per account (dot-free form) |
| send code | `GET /api/verification?email=…` | 6-char code |
| register | `POST /api/user/register` | 429 → wait, retry the same fresh code |
| login | `POST /api/user/login` | session token |
| check-in | `POST /api/user/checkin` | the daily bonus |
| key | `POST /api/token/` (+ reveal) | written once |
| proof | `POST /v1/chat/completions` | 200 with content = the key is live |

## ⚙️ CLI options

```bash
node src/cli.js --count N        # mint N keys (check-in + chat gate per account)
node src/cli.js --group default
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Env | Default | Meaning |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | exit pool, one line per proxy |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | model for the live verification |

## 🛰️ Behind a proxy

**Optional — recommended at scale (per-IP limits, bans).** The tool works without a proxy too.

- **With a pool:** pass `--pool <file>` (or set `SUANLICAT_PROXIES`) and every request goes through an exit from that list — a dead line fails that attempt and the tool moves to the next one (fail-closed while a pool is provided; the pool is re-probed at use).
- **Without a pool:** the CLI prints `running WITHOUT a proxy (direct)` and connects directly. Nothing about sign-up, check-in, key minting or verification changes — only where the packets leave from.
- Run `--screen` against either mode: it tests the exits you have (or the direct route) against the provider before a real run.

## 📄 Files

| Path | Contents |
| --- | --- |
| `keys.txt` | one live-verified key per line — git-ignored |
| `accounts.json` | username / e-mail / quotaStart / quotaAfterCheckin / group |
| `proxies.txt` | your exits — git-ignored |
| `outputs/` | run logs — git-ignored |

## 🔌 Connect to 9Router

[9Router](https://9router.com) is a self-hosted, OpenAI-compatible router:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Defaults: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Models are served as `suanlicat/<model id>`.

## 🔑 What one key gets you

| | |
| --- | --- |
| 🏢 Provider | **SuanliCat (算力狐)** — [suanlicat.top](https://suanlicat.top) |
| 🎁 What the provider gives | $1 (100,000 units) on signup + ~13k/day check-in |
| 🔑 What one key gets you | one key on its own account, chat-verified on `deepseek-v4.1-flash` |
| 🌐 API shape | OpenAI-compatible: `POST https://suanlicat.top/v1/chat/completions` |
| 🔒 Your data | only `suanlicat.top` and your inbox backend are contacted |

## 🐞 Issues

If SuanliCat changes its register gates or the check-in reward, the tool stops there and says so. **[Open an issue](../../issues)** with the command and the exact output.

## 📄 License

MIT © 2026 — see [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey contributors

</div>
