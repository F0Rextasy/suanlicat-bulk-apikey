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

**Otomatik oluşturulan API anahtarlarıyla toplu SuanliCat hesabı oluşturma — OpenAI uyumlu çıkarım + 9Router bağlayıcısı.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Hızlı başlangıç](#-quick-start) · [🎁 Ücretsiz katman](#-free-tier--how-much-you-get) · [🤖 Modeller](#-models--all-17-of-them) · [🧪 API'yi kullanma](#-how-to-use-the-api) · [✅ Doğrulandı](#-verified-working--every-key-is-tested) · [🧭 Nasıl çalışır](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Seçenekler](#️-cli-options) · [🛰️ Proxy'ler](#️-behind-a-proxy) · [🐞 Sorunlar](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Bu neden var

SuanliCat (算力狐) **her yeni hesaba 100.000 kota birimi (qpu=100.000 iken ≈ $1) verir** ve üstüne **günlük ~13.270 birim giriş ödemesi** yapar. Kayıt e-posta + 6 karakterli kod ile yapılır, captcha yoktur; canlı anahtar hattı `deepseek-v4.1-flash`'tir ve tüm araç uçtan uca **100 hesap → 100 doğrulanmış anahtar** üretilerek kanıtlanmıştır.

## 🎁 Ücretsiz katman — ne kadar alıyorsunuz

| Ne | Değer |
| --- | --- |
| Kayıt kredisi (`quotaStart`) | **100.000 birim ≈ $1** (qpu=100.000) |
| Günlük giriş | **~+13.270 birim/gün** (ölçüldü; CLI anahtarını saklamadan önce her hesap için bir kez giriş yapar) |
| Kayıt | e-posta + 6 karakterli kod — **captcha yok** (`turnstile_check: false`) |
| Katalog | canlı fiyat listesinde 17 model girdisi |
| Hız limitleri | yoğunluk altında kayıt `429` döner (`register refused`); CLI ~25 sn bekler ve aynı taze kodla yeniden dener |
| Ölçeklenen birim | **bir hesap = bir anahtar** |

> 💡 Ücretsiz katman SuanliCat (算力狐) tarafından belirlenir ve değişebilir. Canlı sayıları [suanlicat.top](https://suanlicat.top) adresinden kontrol edin — ve kararı aracın kendi kapısına bırakın:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modeller — 17'sinin tamamı

Canlı fiyat listesi **17 model girdisi** içerir (`qwen-turbo`, `doubao-code`, `doubao-pro-max`, `coze-*`, `deepseek-*` …). Doğrulanmış anahtar hattı:

| Model Kimliği | Doğrulandı |
| --- | --- |
| `deepseek-v4.1-flash` | `providers/suanlicat/keys.txt` üretimi sırasında **her anahtar, chat-200**; gönderilen CLI ile bağımsız yeniden kontrol (bkz. Doğrulandı tablosu) |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 API nasıl kullanılır

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
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

Bir anahtar `keys.txt` dosyasına **yalnızca** `/v1/chat/completions` gerçek içerikle 200 yanıt verdikten sonra yazılır; `accounts.json` kullanıcı adı / e-posta / quotaStart → quotaAfterCheckin / grup bilgilerini saklar.

### 🖱️ Başlatıcı

Başlatıcı proxy listesini ve sayıyı sorar, bunları hatırlar ve CLI'yi çalıştırır — üret, doğrula, çıkışları tara, ayarlar, yardım, 9Router'a Bağlan.

## ✅ Çalıştığı doğrulandı — her anahtar test edilir

| Kontrol | Sonuç |
| --- | --- |
| `GET /api/status` | **200** — kayıt açık, e-posta doğrulama açık, captcha kapalı, giriş açık |
| `POST /api/user/checkin` | **200** — günlük bonus hesaba geçer |
| `POST /api/token/` (+ reveal) | **200** — anahtar materyali bir kez döner |
| `POST /v1/chat/completions` (`deepseek-v4.1-flash`) — yazılmadan önce **her** anahtar için | gerçek içerikle **200** — 100 anahtarlık üretim 100/100 ile tamamlandı |
| bağımsız yeniden kontrol — gönderilen CLI ile **her anahtar doğrulandı** (`--verify-only`, taze çıkış, `deepseek-v4.1-flash` chat-200, 2026-10-20) |
| panel tarafı notu | kayıt yoğunluk altında `429` ile kısıtlar — CLI ~25 sn bekler ve yeniden dener; hiç gelmeyen bir posta (paylaşılan geçici posta alan adları) bir sonraki kutuya geçer |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **Kalite kapıları.** Buradaki her anahtar `keys.txt` dosyasına yazılmadan önce canlı bir `chat-200` geçti
> (ölü anahtarlar asla gönderilmez). Yönlendirici kombinasyonları model düzeyinde aynı kuralı izler: bir girdi
> **yalnızca model `chat-200` yanıtlıyor ve araçları çağırabiliyorsa** eklenir — araç desteği olmayan bir model,
> ölü bir anahtar veya ölü bir sağlayıcı eklenmek yerine çıkarılır. Doğrulama adımını istediğiniz zaman yeniden çalıştırın;
> hatalar bildirilir, asla gizlenmez.

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

| Ortam | Varsayılan | Anlam |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | çıkış havuzu, her satırda bir proxy |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | canlı doğrulama için model |

## 🛰️ Proxy arkasında

**İsteğe bağlı — ölçekte önerilir (IP başına limitler, yasaklar).** Araç proxy olmadan da çalışır.

- **Havuzla:** `--pool <file>` geçin (veya `SUANLICAT_PROXIES` ayarlayın); her istek o listeden bir çıkıştan geçer — ölü bir hat o denemeyi başarısız kılar ve araç bir sonrakine geçer (havuz sağlandığında fail-closed; havuz kullanım anında yeniden yoklanır).
- **Havuz olmadan:** CLI `running WITHOUT a proxy (direct)` yazdırır ve doğrudan bağlanır. Kayıt, günlük giriş, anahtar üretimi veya doğrulama konusunda hiçbir şey değişmez — yalnızca paketlerin nereden çıktığı değişir.
- Her iki moda karşı `--screen` çalıştırın: gerçek bir çalıştırmadan önce sahip olduğunuz çıkışları (veya doğrudan rotayı) sağlayıcıya karşı test eder.

## 📄 Dosyalar

| Yol | İçerik |
| --- | --- |
| `keys.txt` | satır başına bir canlı doğrulanmış anahtar — git tarafından yok sayılır |
| `accounts.json` | kullanıcı adı / e-posta / quotaStart / quotaAfterCheckin / grup |
| `proxies.txt` | çıkışlarınız — git tarafından yok sayılır |
| `outputs/` | çalıştırma günlükleri — git tarafından yok sayılır |

## 🔌 9Router'a bağlan

[9Router](https://9router.com) kendi sunucunuzda barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Varsayılanlar: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Modeller `suanlicat/<model id>` olarak sunulur.

## 🔌 OmniRoute'a bağlan

[OmniRoute](https://omniroute.online) kendi sunucunuzda barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Varsayılanlar: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Modeller `suanlicat/<model id>` olarak sunulur.

## 🔑 Bir anahtar size ne kazandırır

| | |
| --- | --- |
| 🏢 Sağlayıcı | **SuanliCat (算力狐)** — [suanlicat.top](https://suanlicat.top) |
| 🎁 Sağlayıcının verdikleri | kayıtta $1 (100.000 birim) + günde ~13k giriş |
| 🔑 Bir anahtar size ne kazandırır | kendi hesabında bir anahtar, `deepseek-v4.1-flash` üzerinde chat ile doğrulanmış |
| 🌐 API biçimi | OpenAI uyumlu: `POST https://suanlicat.top/v1/chat/completions` |
| 🔒 Verileriniz | yalnızca `suanlicat.top` ve posta kutunuzun arka ucu ile iletişim kurulur |

## 🐞 Sorunlar

SuanliCat kayıt kapılarını veya günlük giriş ödülünü değiştirirse, araç orada durur ve bunu bildirir. Komut ve tam çıktı ile birlikte **[bir sorun açın](../../issues)**.

## 📄 Lisans

MIT © 2026 — bkz. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey katkıda bulunanlar

</div>