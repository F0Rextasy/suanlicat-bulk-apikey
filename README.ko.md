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

**자동 생성된 API 키로 SuanliCat 계정을 대량 생성 — OpenAI 호환 추론 + 9Router 커넥터.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 빠른 시작](#-quick-start) · [🎁 무료 티어](#-free-tier--how-much-you-get) · [🤖 모델](#-models--all-17-of-them) · [🧪 API 사용하기](#-how-to-use-the-api) · [✅ 검증됨](#-verified-working--every-key-is-tested) · [🧭 작동 방식](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ 옵션](#️-cli-options) · [🛰️ 프록시](#️-behind-a-proxy) · [🐞 이슈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ 이 도구가 존재하는 이유

SuanliCat (算力狐)은 **모든 신규 계정에 100,000 쿼터 단위(≈ $1, qpu=100,000 기준)를 지급**하며, 추가로 **매일 약 13,270 단위의 출석 체크 보너스**를 지급합니다. 가입은 이메일 + 6자리 코드 방식이며 캡차가 없습니다. 실제 사용되는 키 라인은 `deepseek-v4.1-flash`이고, 이 도구는 **100개 계정 생성 → 100개 키 검증**으로 처음부터 끝까지 입증되었습니다.

## 🎁 무료 티어 — 얼마나 받을 수 있나

| 항목 | 값 |
| --- | --- |
| 가입 크레딧 (`quotaStart`) | **100,000 단위 ≈ $1** (qpu=100,000) |
| 일일 출석 체크 | **~+13,270 단위/일** (측정값; CLI는 키를 보관하기 전에 계정당 한 번 출석 체크를 수행) |
| 가입 | 이메일 + 6자리 코드 — **캡차 없음** (`turnstile_check: false`) |
| 카탈로그 | 실시간 가격표에 17개 모델 항목 |
| 속도 제한 | 버스트 상황에서 register가 `429`를 반환 (`register refused`); CLI는 약 25초 대기 후 동일한 새 코드로 재시도 |
| 확장되는 단위 | **계정 하나 = 키 하나** |

> 💡 무료 티어는 SuanliCat (算力狐)이 설정하며 변경될 수 있습니다. 실시간 수치는 [suanlicat.top](https://suanlicat.top)에서 확인하세요 — 그리고 도구 자체의 게이트가 판단하도록 하세요:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 모델 — 17개 전부

실시간 가격표에는 **17개 모델 항목**(`qwen-turbo`, `doubao-code`, `doubao-pro-max`, `coze-*`, `deepseek-*` …)이 있습니다. 검증된 키 라인:

| 모델 ID | 검증됨 |
| --- | --- |
| `deepseek-v4.1-flash` | `providers/suanlicat/keys.txt`에서 생성 중 **100개 키 chat-200**; 제공된 CLI를 통한 독립 재검증 (검증됨 표 참조) |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 API 사용 방법

Base URL **`https://suanlicat.top/v1`** — OpenAI 호환.

```python
from openai import OpenAI
c = OpenAI(base_url="https://suanlicat.top/v1", api_key="beoLYs…")
print(c.chat.completions.create(model="deepseek-v4.1-flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 빠른 시작

```bash
cd suanlicat-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

키는 `/v1/chat/completions`가 실제 콘텐츠와 함께 200을 반환한 **후에만** `keys.txt`에 기록됩니다. `accounts.json`은 username / e-mail / quotaStart → quotaAfterCheckin / group을 보관합니다.

### 🖱️ 런처

런처는 프록시 목록과 개수를 묻고, 이를 기억한 뒤 CLI를 실행합니다 — mint, verify, screen exits, settings, help, Connect to 9Router.

## ✅ 검증된 작동 — 모든 키가 테스트됨

| 검사 | 결과 |
| --- | --- |
| `GET /api/status` | **200** — register 켜짐, 이메일 인증 켜짐, 캡차 꺼짐, 출석 체크 켜짐 |
| `POST /api/user/checkin` | **200** — 일일 보너스가 계정에 적립됨 |
| `POST /api/token/` (+ reveal) | **200** — 키 자료가 한 번 반환됨 |
| `POST /v1/chat/completions` (`deepseek-v4.1-flash`) — 기록되기 전 **모든** 키에 대해 | 실제 콘텐츠와 함께 **200** — 100개 키 생성이 100/100으로 완료됨 |
| 독립 재검증 — 제공된 CLI를 통해 **100/100 키 활성** (`--verify-only`, 새 실행, `deepseek-v4.1-flash` chat-200, 2026-10-20) |
| 패널 측 참고 | 버스트 상황에서 register가 `429`로 제한 — CLI는 약 25초 대기 후 재시도; 도착하지 않는 메일(공유 임시 메일 도메인)은 다음 메일함으로 이동 |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 작동 방식

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: SuanliCat (算力狐) page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://suanlicat.top/v1/chat/completions → real content = the key is live
```

| 단계 | 호출 | 비고 |
| --- | --- | --- |
| status | `GET /api/status` | register/이메일/출석 체크 게이트 (캡차 꺼짐) |
| mailbox | Emailnator `POST /api/generate-email` | 계정당 새 @gmail.com 하나 (점 없는 형식) |
| send code | `GET /api/verification?email=…` | 6자리 코드 |
| register | `POST /api/user/register` | 429 → 대기, 동일한 새 코드로 재시도 |
| login | `POST /api/user/login` | 세션 토큰 |
| check-in | `POST /api/user/checkin` | 일일 보너스 |
| key | `POST /api/token/` (+ reveal) | 한 번 기록됨 |
| proof | `POST /v1/chat/completions` | 콘텐츠와 함께 200 = 키가 활성 |

## ⚙️ CLI 옵션

```bash
node src/cli.js --count N        # mint N keys (check-in + chat gate per account)
node src/cli.js --group default
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| 환경 변수 | 기본값 | 의미 |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | 출구 풀, 프록시당 한 줄 |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | 실시간 검증에 사용할 모델 |

## 🛰️ 프록시 뒤에서

**선택 사항 — 대규모에서는 권장 (IP별 제한, 차단).** 이 도구는 프록시 없이도 작동합니다.

- **풀 사용 시:** `--pool <file>`을 전달하거나 `SUANLICAT_PROXIES`를 설정하면 모든 요청이 해당 목록의 출구를 통해 나갑니다 — 죽은 라인은 해당 시도를 실패시키고 도구는 다음으로 넘어갑니다 (풀이 제공되면 fail-closed; 풀은 사용 시점에 재탐색됨).
- **풀 없이:** CLI는 `running WITHOUT a proxy (direct)`를 출력하고 직접 연결합니다. 가입, 출석 체크, 키 생성, 검증에 대해 달라지는 것은 없습니다 — 패킷이 나가는 위치만 다를 뿐입니다.
- 어느 모드에서든 `--screen`을 실행하세요: 실제 실행 전에 보유한 출구(또는 직접 경로)를 제공자에 대해 테스트합니다.

## 📄 파일

| 경로 | 내용 |
| --- | --- |
| `keys.txt` | 한 줄에 하나의 실시간 검증된 키 — git-ignored |
| `accounts.json` | username / e-mail / quotaStart / quotaAfterCheckin / group |
| `proxies.txt` | 사용자의 출구 — git-ignored |
| `outputs/` | 실행 로그 — git-ignored |

## 🔌 9Router에 연결

[9Router](https://9router.com)는 자체 호스팅, OpenAI 호환 라우터입니다:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

기본값: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. 모델은 `suanlicat/<model id>`로 제공됩니다.

## 🔑 키 하나로 얻는 것

| | |
| --- | --- |
| 🏢 제공자 | **SuanliCat (算力狐)** — [suanlicat.top](https://suanlicat.top) |
| 🎁 제공자가 주는 것 | 가입 시 $1 (100,000 단위) + 일일 약 13k 출석 체크 |
| 🔑 키 하나로 얻는 것 | 자체 계정의 키 하나, `deepseek-v4.1-flash`에서 채팅 검증됨 |
| 🌐 API 형태 | OpenAI 호환: `POST https://suanlicat.top/v1/chat/completions` |
| 🔒 데이터 | `suanlicat.top`과 사용자의 메일함 백엔드만 접촉됨 |

## 🐞 이슈

SuanliCat이 register 게이트나 출석 체크 보상을 변경하면, 도구는 그 지점에서 멈추고 그 사실을 알립니다. 명령과 정확한 출력을 포함하여 **[이슈를 열어주세요](../../issues)**.

## 📄 라이선스

MIT © 2026 — [LICENSE](LICENSE) 참조.

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey contributors

</div>