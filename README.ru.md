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

**Массовое создание аккаунтов SuanliCat с автоматически сгенерированными API-ключами — инференс, совместимый с OpenAI, + коннектор 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Быстрый старт](#-quick-start) · [🎁 Бесплатный тариф](#-free-tier--how-much-you-get) · [🤖 Модели](#-models--all-17-of-them) · [🧪 Использование API](#-how-to-use-the-api) · [✅ Проверено](#-verified-working--every-key-is-tested) · [🧭 Как это работает](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Опции](#️-cli-options) · [🛰️ Прокси](#️-behind-a-proxy) · [🐞 Проблемы](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Зачем это нужно

SuanliCat (算力狐) выдаёт **100 000 единиц квоты (≈ $1 при qpu=100 000) каждому новому аккаунту** и дополнительно начисляет **ежедневный чек-ин на ~13 270 единиц**. Регистрация — e-mail + 6-символьный код, без капчи; рабочая линейка ключей — `deepseek-v4.1-flash`, и весь инструмент был проверен от начала до конца путём создания **100 аккаунтов → 100 проверенных ключей**.

## 🎁 Бесплатный тариф — сколько вы получаете

| Что | Значение |
| --- | --- |
| Кредит при регистрации (`quotaStart`) | **100 000 единиц ≈ $1** (qpu=100 000) |
| Ежедневный чек-ин | **~+13 270 единиц/день** (измерено; CLI выполняет чек-ин один раз на аккаунт перед сохранением ключа) |
| Регистрация | e-mail + 6-символьный код — **без капчи** (`turnstile_check: false`) |
| Каталог | 17 записей моделей в актуальном прайс-листе |
| Ограничения скорости | регистрация отвечает `429` при всплесках (`register refused`); CLI делает паузу ~25 с и повторяет с тем же свежим кодом |
| Единица, которая масштабируется | **один аккаунт = один ключ** |

> 💡 Бесплатный тариф устанавливается SuanliCat (算力狐) и может измениться. Проверяйте актуальные цифры на [suanlicat.top](https://suanlicat.top) — и пусть собственный шлюз инструмента решит:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Модели — все 17

Актуальный прайс-лист содержит **17 записей моделей** (`qwen-turbo`, `doubao-code`, `doubao-pro-max`, `coze-*`, `deepseek-*` …). Проверенная линейка ключей:

| ID модели | Проверено |
| --- | --- |
| `deepseek-v4.1-flash` | **100 ключей chat-200** во время создания в `providers/suanlicat/keys.txt`; независимая перепроверка через поставляемый CLI (см. таблицу «Проверено») |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Как использовать API

Базовый URL **`https://suanlicat.top/v1`** — совместим с OpenAI.

```python
from openai import OpenAI
c = OpenAI(base_url="https://suanlicat.top/v1", api_key="beoLYs…")
print(c.chat.completions.create(model="deepseek-v4.1-flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Быстрый старт

```bash
cd suanlicat-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings
# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

Ключ записывается в `keys.txt` **только** после того, как `/v1/chat/completions` ответит 200 с реальным содержимым; `accounts.json` хранит username / e-mail / quotaStart → quotaAfterCheckin / group.

### 🖱️ Лаунчер

Лаунчер запрашивает список прокси и количество, запоминает их и запускает CLI — создание, проверка, тест выходов, настройки, справка, подключение к 9Router.

## ✅ Проверено в работе — каждый ключ протестирован

| Проверка | Результат |
| --- | --- |
| `GET /api/status` | **200** — регистрация включена, подтверждение e-mail включено, капча выключена, чек-ин включён |
| `POST /api/user/checkin` | **200** — ежедневный бонус зачисляется на аккаунт |
| `POST /api/token/` (+ reveal) | **200** — материал ключа возвращается один раз |
| `POST /v1/chat/completions` (`deepseek-v4.1-flash`) — для **каждого** ключа перед его записью | **200** с реальным содержимым — создание 100 ключей завершилось со счётом 100/100 |
| независимая перепроверка — **100/100 ключей работают** через поставляемый CLI (`--verify-only`, свежий запуск, `deepseek-v4.1-flash` chat-200, 2026-10-20) |
| примечание со стороны панели | регистрация ограничивает запросы `429` при всплеске — CLI ждёт ~25 с и повторяет; письмо, которое так и не приходит (общие домены временной почты), приводит к переходу к следующему ящику |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 Как это работает

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: SuanliCat (算力狐) page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://suanlicat.top/v1/chat/completions → real content = the key is live
```

| Шаг | Запрос | Примечания |
| --- | --- | --- |
| status | `GET /api/status` | шлюзы регистрации/e-mail/чек-ина (капча выключена) |
| mailbox | Emailnator `POST /api/generate-email` | один свежий @gmail.com на аккаунт (форма без точек) |
| send code | `GET /api/verification?email=…` | 6-символьный код |
| register | `POST /api/user/register` | 429 → подождать, повторить с тем же свежим кодом |
| login | `POST /api/user/login` | токен сессии |
| check-in | `POST /api/user/checkin` | ежедневный бонус |
| key | `POST /api/token/` (+ reveal) | записывается один раз |
| proof | `POST /v1/chat/completions` | 200 с содержимым = ключ работает |

## ⚙️ Опции CLI

```bash
node src/cli.js --count N        # mint N keys (check-in + chat gate per account)
node src/cli.js --group default
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Переменная | По умолчанию | Значение |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | пул выходов, одна строка на прокси |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | модель для живой проверки |

## 🛰️ За прокси

**Необязательно — рекомендуется при масштабе (лимиты на IP, баны).** Инструмент работает и без прокси.

- **С пулом:** передайте `--pool <file>` (или задайте `SUANLICAT_PROXIES`), и каждый запрос идёт через выход из этого списка — мёртвая линия проваливает эту попытку, и инструмент переходит к следующей (fail-closed, пока пул предоставлен; пул перепроверяется при использовании).
- **Без пула:** CLI выводит `running WITHOUT a proxy (direct)` и подключается напрямую. Ничего в регистрации, чек-ине, создании ключей или проверке не меняется — только то, откуда уходят пакеты.
- Запустите `--screen` для любого режима: он тестирует имеющиеся у вас выходы (или прямой маршрут) против провайдера перед реальным запуском.

## 📄 Файлы

| Путь | Содержимое |
| --- | --- |
| `keys.txt` | один проверенный в работе ключ на строку — игнорируется git |
| `accounts.json` | username / e-mail / quotaStart / quotaAfterCheckin / group |
| `proxies.txt` | ваши выходы — игнорируется git |
| `outputs/` | логи запусков — игнорируется git |

## 🔌 Подключение к 9Router

[9Router](https://9router.com) — это самостоятельно размещаемый маршрутизатор, совместимый с OpenAI:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

По умолчанию: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Модели предоставляются как `suanlicat/<model id>`.

## 🔑 Что даёт один ключ

| | |
| --- | --- |
| 🏢 Провайдер | **SuanliCat (算力狐)** — [suanlicat.top](https://suanlicat.top) |
| 🎁 Что даёт провайдер | $1 (100 000 единиц) при регистрации + ~13k/день за чек-ин |
| 🔑 Что даёт один ключ | один ключ на собственном аккаунте, проверенный в чате на `deepseek-v4.1-flash` |
| 🌐 Форма API | Совместим с OpenAI: `POST https://suanlicat.top/v1/chat/completions` |
| 🔒 Ваши данные | связь только с `suanlicat.top` и вашим бэкендом почтового ящика |

## 🐞 Проблемы

Если SuanliCat изменит свои шлюзы регистрации или награду за чек-ин, инструмент остановится на этом месте и сообщит об этом. **[Откройте issue](../../issues)** с командой и точным выводом.

## 📄 Лицензия

MIT © 2026 — см. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey contributors

</div>