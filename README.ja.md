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

**自動生成された API キーで SuanliCat アカウントを一括作成 — OpenAI 互換の推論 + 9Router コネクタ。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 クイックスタート](#-quick-start) · [🎁 無料枠](#-free-tier--how-much-you-get) · [🤖 モデル](#-models--all-17-of-them) · [🧪 API の使い方](#-how-to-use-the-api) · [✅ 検証済み](#-verified-working--every-key-is-tested) · [🧭 仕組み](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ オプション](#️-cli-options) · [🛰️ プロキシ](#️-behind-a-proxy) · [🐞 問題報告](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ これが存在する理由

SuanliCat（算力狐）は**新規アカウントごとに 100,000 クォータ単位（qpu=100,000 で約 $1）**を配布し、さらに**毎日のチェックインで約 13,270 単位**を支払います。登録はメールアドレス + 6 文字のコードで、キャプチャなし。稼働中のキーラインは `deepseek-v4.1-flash` で、ツール全体は**100 アカウント → 100 個の検証済みキー**を発行してエンドツーエンドで実証されました。

## 🎁 無料枠 — どれだけ得られるか

| 項目 | 値 |
| --- | --- |
| 登録クレジット（`quotaStart`） | **100,000 単位 ≈ $1**（qpu=100,000） |
| 毎日のチェックイン | **約 +13,270 単位/日**（実測値。CLI はキーを保持する前にアカウントごとに 1 回チェックインします） |
| 登録 | メールアドレス + 6 文字のコード — **キャプチャなし**（`turnstile_check: false`） |
| カタログ | 稼働中の料金表に 17 のモデルエントリ |
| レート制限 | バースト時に register が `429` を返します（`register refused`）。CLI は約 25 秒バックオフし、同じ新しいコードで再試行します |
| スケールする単位 | **1 アカウント = 1 キー** |

> 💡 無料枠は SuanliCat（算力狐）が設定しており、変更される可能性があります。最新の数値は [suanlicat.top](https://suanlicat.top) で確認してください — そしてツール自身のゲートに判断させましょう：

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 モデル — 全 17 種

稼働中の料金表には **17 のモデルエントリ**（`qwen-turbo`、`doubao-code`、`doubao-pro-max`、`coze-*`、`deepseek-*` …）が含まれています。検証済みのキーライン：

| モデル ID | 検証済み |
| --- | --- |
| `deepseek-v4.1-flash` | `providers/suanlicat/keys.txt` での発行時に **100 キーが chat-200**。同梱の CLI による独立した再チェック（検証済みテーブルを参照） |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 API の使い方

ベース URL **`https://suanlicat.top/v1`** — OpenAI 互換。

```python
from openai import OpenAI
c = OpenAI(base_url="https://suanlicat.top/v1", api_key="beoLYs…")
print(c.chat.completions.create(model="deepseek-v4.1-flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 クイックスタート

```bash
cd suanlicat-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings
# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

キーは `/v1/chat/completions` が実際のコンテンツとともに 200 を返した**後にのみ** `keys.txt` に書き込まれます。`accounts.json` は username / e-mail / quotaStart → quotaAfterCheckin / group を保持します。

### 🖱️ ランチャー

ランチャーはプロキシリストと件数を尋ね、それらを記憶して CLI を実行します — mint、verify、screen exits、settings、help、Connect to 9Router。

## ✅ 動作検証済み — すべてのキーがテストされています

| チェック | 結果 |
| --- | --- |
| `GET /api/status` | **200** — register オン、メール認証 オン、キャプチャ オフ、チェックイン オン |
| `POST /api/user/checkin` | **200** — 毎日のボーナスがアカウントに付与されます |
| `POST /api/token/`（+ reveal） | **200** — キーマテリアルが一度だけ返されます |
| `POST /v1/chat/completions`（`deepseek-v4.1-flash`）— 書き込まれる前の**すべての**キーに対して | 実際のコンテンツとともに **200** — 100 キーの発行が 100/100 で完了 |
| 独立した再チェック — 同梱の CLI 経由で **100/100 キーが稼働**（`--verify-only`、新規終了、`deepseek-v4.1-flash` chat-200、2026-10-20） |
| パネル側の注記 | register はバースト時に `429` でスロットルします — CLI は約 25 秒待って再試行します。届かないメール（共有の一時メールドメイン）は次のボックスに移ります |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 仕組み

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: SuanliCat (算力狐) page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://suanlicat.top/v1/chat/completions → real content = the key is live
```

| ステップ | 呼び出し | 備考 |
| --- | --- | --- |
| status | `GET /api/status` | register/メール/チェックインのゲート（キャプチャ オフ） |
| mailbox | Emailnator `POST /api/generate-email` | アカウントごとに 1 つの新しい @gmail.com（ドットなし形式） |
| send code | `GET /api/verification?email=…` | 6 文字のコード |
| register | `POST /api/user/register` | 429 → 待機し、同じ新しいコードで再試行 |
| login | `POST /api/user/login` | セッショントークン |
| check-in | `POST /api/user/checkin` | 毎日のボーナス |
| key | `POST /api/token/`（+ reveal） | 一度だけ書き込み |
| proof | `POST /v1/chat/completions` | コンテンツとともに 200 = キーが稼働中 |

## ⚙️ CLI オプション

```bash
node src/cli.js --count N        # mint N keys (check-in + chat gate per account)
node src/cli.js --group default
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| 環境変数 | デフォルト | 意味 |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | 出口プール、プロキシごとに 1 行 |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | ライブ検証に使用するモデル |

## 🛰️ プロキシの背後で

**任意 — 大規模運用では推奨（IP ごとの制限、BAN）。** このツールはプロキシなしでも動作します。

- **プールあり：** `--pool <file>` を渡す（または `SUANLICAT_PROXIES` を設定する）と、すべてのリクエストがそのリストの出口を経由します — 死んだ回線はその試行を失敗させ、ツールは次の回線に移ります（プールが提供されている間はフェイルクローズ。プールは使用時に再プローブされます）。
- **プールなし：** CLI は `running WITHOUT a proxy (direct)` と表示し、直接接続します。登録、チェックイン、キー発行、検証のいずれも変わりません — 変わるのはパケットが出ていく場所だけです。
- どちらのモードでも `--screen` を実行してください：実際の実行前に、持っている出口（または直接ルート）をプロバイダーに対してテストします。

## 📄 ファイル

| パス | 内容 |
| --- | --- |
| `keys.txt` | 1 行につき 1 つのライブ検証済みキー — git 無視 |
| `accounts.json` | username / e-mail / quotaStart / quotaAfterCheckin / group |
| `proxies.txt` | あなたの出口 — git 無視 |
| `outputs/` | 実行ログ — git 無視 |

## 🔌 9Router に接続

[9Router](https://9router.com) はセルフホスト型の OpenAI 互換ルーターです：

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

デフォルト：`--prefix suanlicat`、`--base-url https://suanlicat.top/v1`、`--model deepseek-v4.1-flash`。モデルは `suanlicat/<model id>` として提供されます。

## 🔑 1 つのキーで得られるもの

| | |
| --- | --- |
| 🏢 プロバイダー | **SuanliCat（算力狐）** — [suanlicat.top](https://suanlicat.top) |
| 🎁 プロバイダーが提供するもの | 登録時に $1（100,000 単位）+ 約 13k/日のチェックイン |
| 🔑 1 つのキーで得られるもの | 独自のアカウント上の 1 つのキー、`deepseek-v4.1-flash` でチャット検証済み |
| 🌐 API 形式 | OpenAI 互換：`POST https://suanlicat.top/v1/chat/completions` |
| 🔒 あなたのデータ | 接触するのは `suanlicat.top` とあなたの受信トレイバックエンドのみ |

## 🐞 問題報告

SuanliCat が登録ゲートやチェックイン報酬を変更した場合、ツールはそこで停止し、その旨を報告します。コマンドと正確な出力を添えて**[issue を作成してください](../../issues)**。

## 📄 ライセンス

MIT © 2026 — [LICENSE](LICENSE) を参照。

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey contributors

</div>