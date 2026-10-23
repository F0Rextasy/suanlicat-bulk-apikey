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

**Crie contas SuanliCat em massa com chaves de API geradas automaticamente — inferência compatível com OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Início rápido](#-quick-start) · [🎁 Nível gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-17-of-them) · [🧪 Use a API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Como funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Opções](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por que isto existe

O SuanliCat (算力狐) distribui **100.000 unidades de cota (≈ $1 com qpu=100.000) para cada nova conta** e ainda paga um **check-in diário de ~13.270 unidades**. O cadastro é e-mail + código de 6 caracteres, sem captcha; a linha de chave ativa é `deepseek-v4.1-flash`, e a ferramenta inteira foi comprovada de ponta a ponta ao gerar **100 contas → 100 chaves verificadas**.

## 🎁 Nível gratuito — quanto você recebe

| O quê | Valor |
| --- | --- |
| Crédito de cadastro (`quotaStart`) | **100.000 unidades ≈ $1** (qpu=100.000) |
| Check-in diário | **~+13.270 unidades/dia** (medido; a CLI faz check-in uma vez por conta antes de guardar sua chave) |
| Cadastro | e-mail + código de 6 caracteres — **sem captcha** (`turnstile_check: false`) |
| Catálogo | 17 entradas de modelo na tabela de preços ativa |
| Limites de taxa | o registro responde `429` em rajadas (`register refused`); a CLI espera ~25 s e tenta novamente com o mesmo código novo |
| A unidade que escala | **uma conta = uma chave** |

> 💡 O nível gratuito é definido pelo SuanliCat (算力狐) e pode mudar. Confira os números ativos em [suanlicat.top](https://suanlicat.top) — e deixe o próprio gate da ferramenta decidir:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelos — todos os 17

A tabela de preços ativa contém **17 entradas de modelo** (`qwen-turbo`, `doubao-code`, `doubao-pro-max`, `coze-*`, `deepseek-*` …). A linha de chave verificada:

| ID do modelo | Verificado |
| --- | --- |
| `deepseek-v4.1-flash` | **todas as chaves, chat-200** durante a geração em `providers/suanlicat/keys.txt`; reverificação independente via a CLI fornecida (veja a tabela Verificado) |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Como usar a API

URL base **`https://suanlicat.top/v1`** — compatível com OpenAI.

```python
from openai import OpenAI
c = OpenAI(base_url="https://suanlicat.top/v1", api_key="beoLYs…")
print(c.chat.completions.create(model="deepseek-v4.1-flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Início rápido

```bash
cd suanlicat-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

Uma chave é gravada em `keys.txt` **somente** depois que `/v1/chat/completions` responde 200 com conteúdo real; `accounts.json` mantém username / e-mail / quotaStart → quotaAfterCheckin / group.

### 🖱️ O launcher

O launcher pede a lista de proxies e a quantidade, memoriza-as e executa a CLI — mint, verify, screen exits, settings, help, Connect to 9Router.

## ✅ Verificado e funcionando — toda chave é testada

| Verificação | Resultado |
| --- | --- |
| `GET /api/status` | **200** — registro ativo, verificação por e-mail ativa, captcha desativado, check-in ativo |
| `POST /api/user/checkin` | **200** — o bônus diário cai na conta |
| `POST /api/token/` (+ reveal) | **200** — o material da chave é retornado uma vez |
| `POST /v1/chat/completions` (`deepseek-v4.1-flash`) — para **toda** chave antes de ser gravada | **200** com conteúdo real — a geração de 100 chaves foi concluída em 100/100 |
| reverificação independente — **toda chave verificada** via a CLI fornecida (`--verify-only`, execução nova, `deepseek-v4.1-flash` chat-200, 2026-10-20) |
| nota do lado do painel | o registro limita com `429` em rajadas — a CLI espera ~25 s e tenta novamente; um e-mail que nunca chega (domínios de temp-mail compartilhados) passa para a próxima caixa |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **Gates de qualidade.** Toda chave aqui passou por um `chat-200` ao vivo antes de ser gravada em `keys.txt`
> (chaves mortas nunca são entregues). As combinações de roteador seguem a mesma regra no nível do modelo: uma entrada é
> adicionada **somente se o modelo responder `chat-200` e conseguir chamar ferramentas** — um modelo sem suporte a ferramentas,
> uma chave morta ou um provedor morto é descartado em vez de adicionado. Reexecute a etapa de verificação a qualquer momento;
> as falhas são reportadas, nunca ocultadas.

## 🧭 Como funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: SuanliCat (算力狐) page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://suanlicat.top/v1/chat/completions → real content = the key is live
```

| Etapa | Chamada | Notas |
| --- | --- | --- |
| status | `GET /api/status` | gates de registro/e-mail/check-in (captcha desativado) |
| mailbox | Emailnator `POST /api/generate-email` | um @gmail.com novo por conta (forma sem pontos) |
| enviar código | `GET /api/verification?email=…` | código de 6 caracteres |
| registrar | `POST /api/user/register` | 429 → esperar, repetir o mesmo código novo |
| login | `POST /api/user/login` | token de sessão |
| check-in | `POST /api/user/checkin` | o bônus diário |
| chave | `POST /api/token/` (+ reveal) | gravada uma vez |
| prova | `POST /v1/chat/completions` | 200 com conteúdo = a chave está ativa |

## ⚙️ Opções da CLI

```bash
node src/cli.js --count N        # mint N keys (check-in + chat gate per account)
node src/cli.js --group default
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Env | Padrão | Significado |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | pool de saída, uma linha por proxy |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | modelo para a verificação ao vivo |

## 🛰️ Atrás de um proxy

**Opcional — recomendado em escala (limites por IP, banimentos).** A ferramenta funciona sem proxy também.

- **Com um pool:** passe `--pool <file>` (ou defina `SUANLICAT_PROXIES`) e toda requisição passa por uma saída dessa lista — uma linha morta faz aquela tentativa falhar e a ferramenta passa para a próxima (fail-closed enquanto um pool é fornecido; o pool é re-testado no uso).
- **Sem um pool:** a CLI imprime `running WITHOUT a proxy (direct)` e conecta diretamente. Nada sobre cadastro, check-in, geração de chaves ou verificação muda — apenas de onde os pacotes saem.
- Execute `--screen` em qualquer um dos modos: ele testa as saídas que você tem (ou a rota direta) contra o provedor antes de uma execução real.

## 📄 Arquivos

| Caminho | Conteúdo |
| --- | --- |
| `keys.txt` | uma chave verificada ao vivo por linha — ignorada pelo git |
| `accounts.json` | username / e-mail / quotaStart / quotaAfterCheckin / group |
| `proxies.txt` | suas saídas — ignorado pelo git |
| `outputs/` | logs de execução — ignorados pelo git |

## 🔌 Conectar ao 9Router

[9Router](https://9router.com) é um roteador auto-hospedado e compatível com OpenAI:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Padrões: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Os modelos são servidos como `suanlicat/<model id>`.

## 🔌 Conectar ao OmniRoute

[OmniRoute](https://omniroute.online) é um roteador auto-hospedado e compatível com OpenAI:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Padrões: `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Os modelos são servidos como `suanlicat/<model id>`.

## 🔑 O que uma chave oferece

| | |
| --- | --- |
| 🏢 Provedor | **SuanliCat (算力狐)** — [suanlicat.top](https://suanlicat.top) |
| 🎁 O que o provedor dá | $1 (100.000 unidades) no cadastro + ~13k/dia de check-in |
| 🔑 O que uma chave oferece | uma chave em sua própria conta, verificada por chat em `deepseek-v4.1-flash` |
| 🌐 Formato da API | Compatível com OpenAI: `POST https://suanlicat.top/v1/chat/completions` |
| 🔒 Seus dados | apenas `suanlicat.top` e seu backend de inbox são contatados |

## 🐞 Problemas

Se o SuanliCat mudar seus gates de registro ou a recompensa de check-in, a ferramenta para ali e avisa. **[Abra uma issue](../../issues)** com o comando e a saída exata.

## 📄 Licença

MIT © 2026 — veja [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey contributors

</div>