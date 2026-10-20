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

**SuanliCat-Konten in großen Mengen erstellen mit automatisch generierten API-Schlüsseln — OpenAI-kompatible Inferenz + 9Router-Connector.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Schnellstart](#-quick-start) · [🎁 Kostenlose Stufe](#-free-tier--how-much-you-get) · [🤖 Modelle](#-models--all-17-of-them) · [🧪 Die API verwenden](#-how-to-use-the-api) · [✅ Verifiziert](#-verified-working--every-key-is-tested) · [🧭 So funktioniert es](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Optionen](#️-cli-options) · [🛰️ Proxys](#️-behind-a-proxy) · [🐞 Probleme](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Warum es das gibt

SuanliCat (算力狐) verteilt **100.000 Quota-Einheiten (≈ 1 $ bei qpu=100.000) an jedes neue Konto** und zahlt zusätzlich einen **täglichen Check-in von ~13.270 Einheiten**. Die Registrierung erfolgt per E-Mail + 6-stelligem Code, ohne Captcha; die aktuelle Schlüsselzeile ist `deepseek-v4.1-flash`, und das gesamte Tool wurde Ende-zu-Ende bewiesen, indem **100 Konten → 100 verifizierte Schlüssel** erzeugt wurden.

## 🎁 Kostenlose Stufe — wie viel du bekommst

| Was | Wert |
| --- | --- |
| Anmeldeguthaben (`quotaStart`) | **100.000 Einheiten ≈ 1 $** (qpu=100.000) |
| Täglicher Check-in | **~+13.270 Einheiten/Tag** (gemessen; die CLI checkt sich einmal pro Konto ein, bevor sie den Schlüssel behält) |
| Registrierung | E-Mail + 6-stelliger Code — **kein Captcha** (`turnstile_check: false`) |
| Katalog | 17 Modelleinträge im aktuellen Preisblatt |
| Ratenlimits | Die Registrierung antwortet mit `429` bei Bursts (`register refused`); die CLI wartet ~25 s und versucht es mit demselben frischen Code erneut |
| Die Einheit, die skaliert | **ein Konto = ein Schlüssel** |

> 💡 Die kostenlose Stufe wird von SuanliCat (算力狐) festgelegt und kann sich ändern. Prüfe die aktuellen Zahlen auf [suanlicat.top](https://suanlicat.top) — und lass das Gate des Tools selbst entscheiden:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelle — alle 17

Das aktuelle Preisblatt enthält **17 Modelleinträge** (`qwen-turbo`, `doubao-code`, `doubao-pro-max`, `coze-*`, `deepseek-*` …). Die verifizierte Schlüsselzeile:

| Modell-ID | Verifiziert |
| --- | --- |
| `deepseek-v4.1-flash` | **100 Schlüssel chat-200** während des Mints unter `providers/suanlicat/keys.txt`; unabhängige Nachprüfung über die mitgelieferte CLI (siehe Verifiziert-Tabelle) |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 So verwendest du die API

Basis-URL **`https://suanlicat.top/v1`** — OpenAI-kompatibel.

```python
from openai import OpenAI
c = OpenAI(base_url="https://suanlicat.top/v1", api_key="beoLYs…")
print(c.chat.completions.create(model="deepseek-v4.1-flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Schnellstart

```bash
cd suanlicat-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings
# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

Ein Schlüssel wird **nur** dann in `keys.txt` geschrieben, wenn `/v1/chat/completions` mit 200 und echtem Inhalt antwortet; `accounts.json` speichert Benutzername / E-Mail / quotaStart → quotaAfterCheckin / Gruppe.

### 🖱️ Der Launcher

Der Launcher fragt nach der Proxy-Liste und der Anzahl, merkt sie sich und führt die CLI aus — Mint, Verifizieren, Screen-Exits, Einstellungen, Hilfe, Connect to 9Router.

## ✅ Verifiziert funktionierend — jeder Schlüssel wird getestet

| Prüfung | Ergebnis |
| --- | --- |
| `GET /api/status` | **200** — Registrierung an, E-Mail-Verifizierung an, Captcha aus, Check-in an |
| `POST /api/user/checkin` | **200** — der tägliche Bonus landet auf dem Konto |
| `POST /api/token/` (+ reveal) | **200** — Schlüsselmaterial wird einmal zurückgegeben |
| `POST /v1/chat/completions` (`deepseek-v4.1-flash`) — für **jeden** Schlüssel, bevor er geschrieben wird | **200** mit echtem Inhalt — der 100-Schlüssel-Mint wurde mit 100/100 abgeschlossen |
| unabhängige Nachprüfung — **100/100 Schlüssel live** über die mitgelieferte CLI (`--verify-only`, frischer Exit, `deepseek-v4.1-flash` chat-200, 2026-10-20) |
| Hinweis von der Panel-Seite | Die Registrierung drosselt mit `429` bei Burst — die CLI wartet ~25 s und versucht es erneut; eine E-Mail, die nie ankommt (gemeinsam genutzte Temp-Mail-Domains), wechselt zur nächsten Box |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 So funktioniert es

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: SuanliCat (算力狐) page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://suanlicat.top/v1/chat/completions → real content = the key is live
```

| Schritt | Aufruf | Hinweise |
| --- | --- | --- |
| Status | `GET /api/status` | Register-/E-Mail-/Check-in-Gates (Captcha aus) |
| Mailbox | Emailnator `POST /api/generate-email` | eine frische @gmail.com pro Konto (punktfreie Form) |
| Code senden | `GET /api/verification?email=…` | 6-stelliger Code |
| Registrieren | `POST /api/user/register` | 429 → warten, denselben frischen Code erneut versuchen |
| Anmelden | `POST /api/user/login` | Sitzungstoken |
| Check-in | `POST /api/user/checkin` | der tägliche Bonus |
| Schlüssel | `POST /api/token/` (+ reveal) | einmal geschrieben |
| Nachweis | `POST /v1/chat/completions` | 200 mit Inhalt = der Schlüssel ist live |

## ⚙️ CLI-Optionen

```bash
node src/cli.js --count N        # mint N keys (check-in + chat gate per account)
node src/cli.js --group default
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Env | Standard | Bedeutung |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | Exit-Pool, eine Zeile pro Proxy |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | Modell für die Live-Verifizierung |

## 🛰️ Hinter einem Proxy

**Optional — bei Skalierung empfohlen (Limits pro IP, Bans).** Das Tool funktioniert auch ohne Proxy.

- **Mit einem Pool:** Übergib `--pool <file>` (oder setze `SUANLICAT_PROXIES`), und jede Anfrage geht über einen Exit aus dieser Liste — eine tote Leitung lässt diesen Versuch fehlschlagen, und das Tool wechselt zur nächsten (fail-closed, solange ein Pool bereitgestellt wird; der Pool wird bei der Verwendung erneut geprüft).
- **Ohne einen Pool:** Die CLI gibt `running WITHOUT a proxy (direct)` aus und verbindet direkt. An Registrierung, Check-in, Schlüsselerstellung oder Verifizierung ändert sich nichts — nur daran, wo die Pakete das Netz verlassen.
- Führe `--screen` gegen beide Modi aus: Es testet die Exits, die du hast (oder die direkte Route), gegen den Anbieter, bevor ein echter Lauf startet.

## 📄 Dateien

| Pfad | Inhalt |
| --- | --- |
| `keys.txt` | ein live-verifizierter Schlüssel pro Zeile — von Git ignoriert |
| `accounts.json` | Benutzername / E-Mail / quotaStart / quotaAfterCheckin / Gruppe |
| `proxies.txt` | deine Exits — von Git ignoriert |
| `outputs/` | Lauf-Logs — von Git ignoriert |

## 🔌 Mit 9Router verbinden

[9Router](https://9router.com) ist ein selbst gehosteter, OpenAI-kompatibler Router:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Standardwerte: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Modelle werden als `suanlicat/<model id>` bereitgestellt.

## 🔑 Was ein Schlüssel dir bringt

| | |
| --- | --- |
| 🏢 Anbieter | **SuanliCat (算力狐)** — [suanlicat.top](https://suanlicat.top) |
| 🎁 Was der Anbieter gibt | 1 $ (100.000 Einheiten) bei der Anmeldung + ~13k/Tag Check-in |
| 🔑 Was ein Schlüssel dir bringt | ein Schlüssel auf einem eigenen Konto, chat-verifiziert auf `deepseek-v4.1-flash` |
| 🌐 API-Form | OpenAI-kompatibel: `POST https://suanlicat.top/v1/chat/completions` |
| 🔒 Deine Daten | Es werden nur `suanlicat.top` und dein Inbox-Backend kontaktiert |

## 🐞 Probleme

Wenn SuanliCat seine Registrierungs-Gates oder die Check-in-Belohnung ändert, stoppt das Tool dort und sagt es dir. **[Öffne ein Issue](../../issues)** mit dem Befehl und der exakten Ausgabe.

## 📄 Lizenz

MIT © 2026 — siehe [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey contributors

</div>