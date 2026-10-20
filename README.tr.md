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

**Otomatik oluşturulan API anahtarlarıyla toplu SuanliCat hesabı açın — OpenAI uyumlu çıkarım + 9Router bağlayıcısı.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Hızlı başlangıç](#-quick-start) · [🎁 Ücretsiz katman](#-free-tier--how-much-you-get) · [🤖 Modeller](#-models--all-17-of-them) · [🧪 API'yi kullanma](#-how-to-use-the-api) · [✅ Doğrulandı](#-verified-working--every-key-is-tested) · [🧭 Nasıl çalışır](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Seçenekler](#️-cli-options) · [🛰️ Proxy'ler](#️-behind-a-proxy) · [🐞 Sorunlar](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Bu neden var

SuanliCat (算力狐) **her yeni hesaba 100.000 kota birimi (qpu=100.000 iken ≈ $1)** verir ve üstüne **günlük ~13.270 birim giriş ödemesi** yapar. Kayıt e-posta + 6 karakterli koddan oluşur, captcha yoktur; canlı anahtar satırı `deepseek-v4.1-flash`'tir ve aracın tamamı uçtan uca **100 hesap → 100 doğrulanmış anahtar** üretilerek kanıtlanmıştır.

## 🎁 Ücretsiz katman — ne kadar alıyorsunuz

| Ne | Değer |
| --- | --- |
| Kayıt kredisi (`quotaStart`) | **100.000 birim ≈ $1** (qpu=100.000) |
| Günlük giriş | **~+13.270 birim/gün** (ölçüldü; CLI anahtarını saklamadan önce hesap başına bir kez giriş yapar) |
| Kayıt | e-posta + 6 karakterli kod — **captcha yok** (`turnstile_check: false`) |
| Katalog | canlı fiyatlandırma sayfasında 17 model girdisi |
| Hız limitleri | yoğunluk altında kayıt `429` yanıtı verir (`register refused`); CLI ~25 sn bekler ve aynı taze kodla yeniden dener |
| Ölçeklenen birim | **bir hesap = bir anahtar** |

> 💡 Ücretsiz katman SuanliCat (算力狐) tarafından belirlenir ve değişebilir. Canlı sayıları [suanlicat.top](https://suanlicat.top) adresinden kontrol edin — ve kararı aracın kendi kapısına bırakın:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modeller — 17'sinin tamamı

Canlı fiyatlandırma sayfasında **17 model girdisi** bulunur (`qwen-turbo`, `doubao-code`, `doubao-pro-max`, `coze-*`, `deepseek-*` …). Doğrulanmış anahtar satırı:

| Model Kimliği | Doğrulandı |
| --- | --- |
| `deepseek-v4.1-flash` | `providers/suanlicat/keys.txt` konumundaki üretim sırasında **100 anahtar chat-200**; gönderilen CLI ile bağımsız yeniden kontrol (bkz. Doğrulandı tablosu) |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 API'yi kullanma

Temel URL **`https://suanlicat.top/v1`** — OpenAI uyumlu.

```python
from openai import OpenAI
c = OpenAI(base_url="https://suanlicat.top/v1", api_key="beoLYs…")
print(c.chat.completions.create(model="deepseek-v4.1-flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Hızlı başlangıç

```bash
cd suanlicat-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt
node src/launcher.js        # interactive: mint / verify / screen / settings
# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

Bir anahtar `keys.txt` dosyasına **yalnızca** `/v1/chat/completions` gerçek içerikle 200 yanıtı verdikten sonra yazılır; `accounts.json` kullanıcı adı / e-posta / quotaStart → quotaAfterCheckin / grup bilgisini saklar.

### 🖱️ Başlatıcı

Başlatıcı proxy listesini ve sayıyı sorar, bunları hatırlar ve CLI'yi çalıştırır — üret, doğrula, çıkışları tara, ayarlar, yardım, 9Router'a Bağlan.

## ✅ Çalıştığı doğrulandı — her anahtar test edilir

| Kontrol | Sonuç |
| --- | --- |
| `GET /api/status` | **200** — kayıt açık, e-posta doğrulama açık, captcha kapalı, giriş açık |
| `POST /api/user/checkin` | **200** — günlük bonus hesaba geçer |
| `POST /api/token/` (+ reveal) | **200** — anahtar materyali bir kez döndürülür |
| `POST /v1/chat/completions` (`deepseek-v4.1-flash`) — yazılmadan önce **her** anahtar için | gerçek içerikle **200** — 100 anahtarlık üretim 100/100 tamamlandı |
| bağımsız yeniden kontrol — gönderilen CLI ile **100/100 anahtar canlı** (`--verify-only`, temiz çıkış, `deepseek-v4.1-flash` chat-200, 2026-10-20) |
| panel tarafı notu | kayıt yoğunluk altında `429` ile sınırlar — CLI ~25 sn bekler ve yeniden dener; hiç ulaşmayan bir e-posta (paylaşılan geçici posta alan adları) sonraki kutuya geçer |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 Nasıl çalışır

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: SuanliCat (算力狐) page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://suanlicat.top/v1/chat/completions → real content = the key is live
```

| Adım | Çağrı | Notlar |
| --- | --- | --- |
| durum | `GET /api/status` | kayıt/e-posta/giriş kapıları (captcha kapalı) |
| posta kutusu | Emailnator `POST /api/generate-email` | hesap başına bir taze @gmail.com (noktasız biçim) |
| kod gönder | `GET /api/verification?email=…` | 6 karakterli kod |
| kayıt | `POST /api/user/register` | 429 → bekle, aynı taze kodla yeniden dene |
| giriş | `POST /api/user/login` | oturum belirteci |
| günlük giriş | `POST /api/user/checkin` | günlük bonus |
| anahtar | `POST /api/token/` (+ reveal) | bir kez yazılır |
| kanıt | `POST /v1/chat/completions` | içerikle 200 = anahtar canlı |

## ⚙️ CLI seçenekleri

```bash
node src/cli.js --count N        # mint N keys (check-in + chat gate per account)
node src/cli.js --group default
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Ortam değişkeni | Varsayılan | Anlamı |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | çıkış havuzu, her satırda bir proxy |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | canlı doğrulama için model |

## 🛰️ Proxy arkasında

**Gerekli — doğrudan bağlantı yedeği yok.** Her istek bir çıkıştan geçer; panelin kayıt sınırlaması, sağlıklı bir havuzun bir çalışmayı ilerleten şey olduğu anlamına gelir.

## 📄 Dosyalar

| Yol | İçerik |
| --- | --- |
| `keys.txt` | satır başına bir canlı doğrulanmış anahtar — git tarafından yok sayılır |
| `accounts.json` | kullanıcı adı / e-posta / quotaStart / quotaAfterCheckin / grup |
| `proxies.txt` | çıkışlarınız — git tarafından yok sayılır |
| `outputs/` | çalıştırma günlükleri — git tarafından yok sayılır |

## 🔌 9Router'a Bağlan

[9Router](https://9router.com) kendi sunucunuzda barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Varsayılanlar: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Modeller `suanlicat/<model id>` olarak sunulur.

## 🔑 Bir anahtar size ne kazandırır

| | |
| --- | --- |
| 🏢 Sağlayıcı | **SuanliCat (算力狐)** — [suanlicat.top](https://suanlicat.top) |
| 🎁 Sağlayıcının verdikleri | kayıtta $1 (100.000 birim) + günde ~13k giriş |
| 🔑 Bir anahtarın size kazandırdığı | kendi hesabında bir anahtar, `deepseek-v4.1-flash` üzerinde sohbet doğrulamalı |
| 🌐 API biçimi | OpenAI uyumlu: `POST https://suanlicat.top/v1/chat/completions` |
| 🔒 Verileriniz | yalnızca `suanlicat.top` ve posta kutusu arka ucunuzla iletişim kurulur |

## 🐞 Sorunlar

SuanliCat kayıt kapılarını veya günlük giriş ödülünü değiştirirse, araç orada durur ve bunu bildirir. Komut ve tam çıktıyla birlikte **[bir sorun açın](../../issues)**.

## 📄 Lisans

MIT © 2026 — bkz. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey katkıda bulunanlar

</div>