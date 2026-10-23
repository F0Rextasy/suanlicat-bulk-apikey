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

**Crea cuentas de SuanliCat en masa con claves API autogeneradas — inferencia compatible con OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Inicio rápido](#-quick-start) · [🎁 Nivel gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-17-of-them) · [🧪 Usar la API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Cómo funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Opciones](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por qué existe esto

SuanliCat (算力狐) entrega **100.000 unidades de cuota (≈ $1 con qpu=100.000) a cada cuenta nueva** y además paga un **check-in diario de ~13.270 unidades**. El registro es correo electrónico + código de 6 caracteres, sin captcha; la línea de clave activa es `deepseek-v4.1-flash`, y toda la herramienta quedó probada de extremo a extremo al acuñar **100 cuentas → 100 claves verificadas**.

## 🎁 Nivel gratuito — cuánto obtienes

| Qué | Valor |
| --- | --- |
| Crédito de registro (`quotaStart`) | **100.000 unidades ≈ $1** (qpu=100.000) |
| Check-in diario | **~+13.270 unidades/día** (medido; la CLI hace check-in una vez por cuenta antes de conservar su clave) |
| Registro | correo electrónico + código de 6 caracteres — **sin captcha** (`turnstile_check: false`) |
| Catálogo | 17 entradas de modelos en la hoja de precios activa |
| Límites de tasa | el registro responde `429` bajo ráfagas (`register refused`); la CLI espera ~25 s y reintenta con el mismo código recién obtenido |
| La unidad que escala | **una cuenta = una clave** |

> 💡 El nivel gratuito lo define SuanliCat (算力狐) y puede cambiar. Consulta los números activos en [suanlicat.top](https://suanlicat.top) — y deja que la propia puerta de la herramienta decida:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelos — los 17

La hoja de precios activa contiene **17 entradas de modelos** (`qwen-turbo`, `doubao-code`, `doubao-pro-max`, `coze-*`, `deepseek-*` …). La línea de clave verificada:

| ID del modelo | Verificado |
| --- | --- |
| `deepseek-v4.1-flash` | **cada clave, chat-200** durante la acuñación en `providers/suanlicat/keys.txt`; reverificación independiente mediante la CLI incluida (consulta la tabla Verificado) |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Cómo usar la API

URL base **`https://suanlicat.top/v1`** — compatible con OpenAI.

```python
from openai import OpenAI
c = OpenAI(base_url="https://suanlicat.top/v1", api_key="beoLYs…")
print(c.chat.completions.create(model="deepseek-v4.1-flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Inicio rápido

```bash
cd suanlicat-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

Una clave se escribe en `keys.txt` **solo** después de que `/v1/chat/completions` responde 200 con contenido real; `accounts.json` conserva usuario / correo electrónico / quotaStart → quotaAfterCheckin / grupo.

### 🖱️ El lanzador

El lanzador pide la lista de proxies y la cantidad, las recuerda y ejecuta la CLI — acuñar, verificar, cribar salidas, ajustes, ayuda, Conectar a 9Router.

## ✅ Verificado y funcionando — cada clave se prueba

| Comprobación | Resultado |
| --- | --- |
| `GET /api/status` | **200** — registro activado, verificación por correo activada, captcha desactivado, check-in activado |
| `POST /api/user/checkin` | **200** — el bono diario llega a la cuenta |
| `POST /api/token/` (+ reveal) | **200** — el material de la clave se devuelve una vez |
| `POST /v1/chat/completions` (`deepseek-v4.1-flash`) — para **cada** clave antes de escribirse | **200** con contenido real — la acuñación de 100 claves se completó a 100/100 |
| reverificación independiente — **cada clave verificada** mediante la CLI incluida (`--verify-only`, salida nueva, `deepseek-v4.1-flash` chat-200, 2026-10-20) |
| nota del panel | el registro limita con `429` bajo ráfaga — la CLI espera ~25 s y reintenta; un correo que nunca llega (dominios de correo temporal compartidos) pasa a la siguiente bandeja |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **Puertas de calidad.** Cada clave aquí pasó un `chat-200` en vivo antes de escribirse en `keys.txt`
> (las claves muertas nunca se entregan). Las combinaciones del router siguen la misma regla a nivel de modelo: una entrada se
> añade **solo si el modelo responde `chat-200` y puede llamar herramientas** — un modelo sin soporte de herramientas,
> una clave muerta o un proveedor muerto se descarta en lugar de añadirse. Vuelve a ejecutar el paso de verificación cuando quieras;
> los fallos se reportan, nunca se ocultan.

## 🧭 Cómo funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: SuanliCat (算力狐) page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://suanlicat.top/v1/chat/completions → real content = the key is live
```

| Paso | Llamada | Notas |
| --- | --- | --- |
| estado | `GET /api/status` | puertas de registro/correo/check-in (captcha desactivado) |
| buzón | Emailnator `POST /api/generate-email` | un @gmail.com nuevo por cuenta (forma sin puntos) |
| enviar código | `GET /api/verification?email=…` | código de 6 caracteres |
| registrar | `POST /api/user/register` | 429 → esperar, reintentar el mismo código recién obtenido |
| iniciar sesión | `POST /api/user/login` | token de sesión |
| check-in | `POST /api/user/checkin` | el bono diario |
| clave | `POST /api/token/` (+ reveal) | se escribe una vez |
| prueba | `POST /v1/chat/completions` | 200 con contenido = la clave está activa |

## ⚙️ Opciones de la CLI

```bash
node src/cli.js --count N        # mint N keys (check-in + chat gate per account)
node src/cli.js --group default
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Variable de entorno | Predeterminado | Significado |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | grupo de salidas, una línea por proxy |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | modelo para la verificación en vivo |

## 🛰️ Detrás de un proxy

**Opcional — recomendado a escala (límites por IP, bloqueos).** La herramienta también funciona sin proxy.

- **Con un grupo:** pasa `--pool <file>` (o define `SUANLICAT_PROXIES`) y cada solicitud sale por una salida de esa lista — una línea muerta hace fallar ese intento y la herramienta pasa a la siguiente (fail-closed mientras se proporciona un grupo; el grupo se vuelve a sondear al usarse).
- **Sin un grupo:** la CLI imprime `running WITHOUT a proxy (direct)` y se conecta directamente. Nada del registro, check-in, acuñación de claves o verificación cambia — solo desde dónde salen los paquetes.
- Ejecuta `--screen` en cualquiera de los dos modos: prueba las salidas que tengas (o la ruta directa) contra el proveedor antes de una ejecución real.

## 📄 Archivos

| Ruta | Contenido |
| --- | --- |
| `keys.txt` | una clave verificada en vivo por línea — ignorada por git |
| `accounts.json` | usuario / correo electrónico / quotaStart / quotaAfterCheckin / grupo |
| `proxies.txt` | tus salidas — ignorado por git |
| `outputs/` | registros de ejecución — ignorados por git |

## 🔌 Conectar a 9Router

[9Router](https://9router.com) es un router autoalojado y compatible con OpenAI:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Predeterminados: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Los modelos se sirven como `suanlicat/<model id>`.

## 🔌 Conectar a OmniRoute

[OmniRoute](https://omniroute.online) es un router autoalojado y compatible con OpenAI:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Predeterminados: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Los modelos se sirven como `suanlicat/<model id>`.

## 🔑 Qué te da una clave

| | |
| --- | --- |
| 🏢 Proveedor | **SuanliCat (算力狐)** — [suanlicat.top](https://suanlicat.top) |
| 🎁 Qué da el proveedor | $1 (100.000 unidades) al registrarte + ~13k/día de check-in |
| 🔑 Qué te da una clave | una clave en su propia cuenta, verificada por chat en `deepseek-v4.1-flash` |
| 🌐 Forma de la API | compatible con OpenAI: `POST https://suanlicat.top/v1/chat/completions` |
| 🔒 Tus datos | solo se contacta con `suanlicat.top` y tu backend de buzón |

## 🐞 Problemas

Si SuanliCat cambia sus puertas de registro o la recompensa del check-in, la herramienta se detiene ahí y lo indica. **[Abre un issue](../../issues)** con el comando y la salida exacta.

## 📄 Licencia

MIT © 2026 — consulta [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey contributors

</div>