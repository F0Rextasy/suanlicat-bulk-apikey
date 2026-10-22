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

**Créez en masse des comptes SuanliCat avec des clés API générées automatiquement — inférence compatible OpenAI + connecteur 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://suanlicat.top)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Démarrage rapide](#-quick-start) · [🎁 Offre gratuite](#-free-tier--how-much-you-get) · [🤖 Modèles](#-models--all-17-of-them) · [🧪 Utiliser l'API](#-how-to-use-the-api) · [✅ Vérifié](#-verified-working--every-key-is-tested) · [🧭 Comment ça marche](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Options](#️-cli-options) · [🛰️ Proxys](#️-behind-a-proxy) · [🐞 Problèmes](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Pourquoi cet outil existe

SuanliCat (算力狐) offre **100 000 unités de quota (≈ 1 $ à qpu=100 000) à chaque nouveau compte** et verse en plus un **check-in quotidien d'environ 13 270 unités**. L'inscription se fait par e-mail + code à 6 caractères, sans captcha ; la ligne de clé active est `deepseek-v4.1-flash`, et l'outil entier a été prouvé de bout en bout en créant **100 comptes → 100 clés vérifiées**.

## 🎁 Offre gratuite — ce que vous obtenez

| Quoi | Valeur |
| --- | --- |
| Crédit d'inscription (`quotaStart`) | **100 000 unités ≈ 1 $** (qpu=100 000) |
| Check-in quotidien | **~+13 270 unités/jour** (mesuré ; le CLI effectue un check-in par compte avant de conserver sa clé) |
| Inscription | e-mail + code à 6 caractères — **sans captcha** (`turnstile_check: false`) |
| Catalogue | 17 entrées de modèles sur la grille tarifaire active |
| Limites de débit | l'inscription répond `429` en cas de rafale (`register refused`) ; le CLI attend ~25 s et réessaie avec le même code frais |
| L'unité qui passe à l'échelle | **un compte = une clé** |

> 💡 L'offre gratuite est définie par SuanliCat (算力狐) et peut changer. Consultez les chiffres en direct sur [suanlicat.top](https://suanlicat.top) — et laissez le contrôle de l'outil décider :

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modèles — les 17

La grille tarifaire active contient **17 entrées de modèles** (`qwen-turbo`, `doubao-code`, `doubao-pro-max`, `coze-*`, `deepseek-*` …). La ligne de clé vérifiée :

| ID du modèle | Vérifié |
| --- | --- |
| `deepseek-v4.1-flash` | **100 clés chat-200** lors de la création dans `providers/suanlicat/keys.txt` ; re-vérification indépendante via le CLI fourni (voir le tableau Vérifié) |

```bash
curl https://suanlicat.top/v1/chat/completions \
  -H "Authorization: Bearer beoLYs…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4.1-flash","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Comment utiliser l'API

URL de base **`https://suanlicat.top/v1`** — compatible OpenAI.

```python
from openai import OpenAI
c = OpenAI(base_url="https://suanlicat.top/v1", api_key="beoLYs…")
print(c.chat.completions.create(model="deepseek-v4.1-flash",
      messages=[{"role":"user","content":"hi"}]).choices[0].message.content)
```

## 🛠 Démarrage rapide

```bash
cd suanlicat-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30    (check-in runs before the chat gate)
```

Une clé n'est écrite dans `keys.txt` **qu'après** que `/v1/chat/completions` ait répondu 200 avec du contenu réel ; `accounts.json` conserve username / e-mail / quotaStart → quotaAfterCheckin / group.

### 🖱️ Le lanceur

Le lanceur demande la liste de proxys et le nombre, les mémorise, et exécute le CLI — création, vérification, test des sorties, paramètres, aide, Connexion à 9Router.

## ✅ Vérifié fonctionnel — chaque clé est testée

| Vérification | Résultat |
| --- | --- |
| `GET /api/status` | **200** — inscription activée, vérification e-mail activée, captcha désactivé, check-in activé |
| `POST /api/user/checkin` | **200** — le bonus quotidien est crédité sur le compte |
| `POST /api/token/` (+ reveal) | **200** — le matériel de clé est renvoyé une fois |
| `POST /v1/chat/completions` (`deepseek-v4.1-flash`) — pour **chaque** clé avant son écriture | **200** avec du contenu réel — la création de 100 clés s'est terminée à 100/100 |
| re-vérification indépendante — **100/100 clés actives** via le CLI fourni (`--verify-only`, sortie fraîche, `deepseek-v4.1-flash` chat-200, 2026-10-20) |
| note côté panneau | l'inscription limite avec `429` en cas de rafale — le CLI attend ~25 s et réessaie ; un e-mail qui n'arrive jamais (domaines de temp-mail partagés) passe à la boîte suivante |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 Comment ça marche

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: SuanliCat (算力狐) page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://suanlicat.top/v1/chat/completions → real content = the key is live
```

| Étape | Appel | Notes |
| --- | --- | --- |
| status | `GET /api/status` | portes inscription/e-mail/check-in (captcha désactivé) |
| boîte mail | Emailnator `POST /api/generate-email` | un nouveau @gmail.com par compte (forme sans point) |
| envoi du code | `GET /api/verification?email=…` | code à 6 caractères |
| inscription | `POST /api/user/register` | 429 → attendre, réessayer avec le même code frais |
| connexion | `POST /api/user/login` | jeton de session |
| check-in | `POST /api/user/checkin` | le bonus quotidien |
| clé | `POST /api/token/` (+ reveal) | écrite une fois |
| preuve | `POST /v1/chat/completions` | 200 avec contenu = la clé est active |

## ⚙️ Options du CLI

```bash
node src/cli.js --count N        # mint N keys (check-in + chat gate per account)
node src/cli.js --group default
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Env | Défaut | Signification |
| --- | --- | --- |
| `SUANLICAT_PROXIES` | `proxies.txt` | pool de sorties, une ligne par proxy |
| `SUANLICAT_MODEL` | `deepseek-v4.1-flash` | modèle pour la vérification en direct |

## 🛰️ Derrière un proxy

**Optionnel — recommandé à grande échelle (limites par IP, bannissements).** L'outil fonctionne aussi sans proxy.

- **Avec un pool :** passez `--pool <file>` (ou définissez `SUANLICAT_PROXIES`) et chaque requête passe par une sortie de cette liste — une ligne morte fait échouer cette tentative et l'outil passe à la suivante (fail-closed tant qu'un pool est fourni ; le pool est re-testé à l'usage).
- **Sans pool :** le CLI affiche `running WITHOUT a proxy (direct)` et se connecte directement. Rien ne change pour l'inscription, le check-in, la création de clés ou la vérification — seul le point de sortie des paquets diffère.
- Lancez `--screen` dans l'un ou l'autre mode : il teste les sorties dont vous disposez (ou la route directe) contre le fournisseur avant une exécution réelle.

## 📄 Fichiers

| Chemin | Contenu |
| --- | --- |
| `keys.txt` | une clé vérifiée en direct par ligne — ignoré par git |
| `accounts.json` | username / e-mail / quotaStart / quotaAfterCheckin / group |
| `proxies.txt` | vos sorties — ignoré par git |
| `outputs/` | journaux d'exécution — ignorés par git |

## 🔌 Connexion à 9Router

[9Router](https://9router.com) est un routeur auto-hébergé compatible OpenAI :

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Valeurs par défaut : `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Les modèles sont servis sous `suanlicat/<model id>`.

## 🔌 Connexion à OmniRoute

[OmniRoute](https://omniroute.online) est un routeur auto-hébergé compatible OpenAI :

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Valeurs par défaut : `--prefix suanlicat`, `--base-url https://suanlicat.top/v1`, `--model deepseek-v4.1-flash`. Les modèles sont servis sous `suanlicat/<model id>`.
## 🔑 Ce qu'une clé vous apporte

| | |
| --- | --- |
| 🏢 Fournisseur | **SuanliCat (算力狐)** — [suanlicat.top](https://suanlicat.top) |
| 🎁 Ce que le fournisseur offre | 1 $ (100 000 unités) à l'inscription + ~13k/jour en check-in |
| 🔑 Ce qu'une clé vous apporte | une clé sur son propre compte, vérifiée par chat sur `deepseek-v4.1-flash` |
| 🌐 Forme de l'API | compatible OpenAI : `POST https://suanlicat.top/v1/chat/completions` |
| 🔒 Vos données | seuls `suanlicat.top` et votre backend de boîte mail sont contactés |

## 🐞 Problèmes

Si SuanliCat modifie ses portes d'inscription ou la récompense de check-in, l'outil s'arrête là et le signale. **[Ouvrez un ticket](../../issues)** avec la commande et la sortie exacte.

## 📄 Licence

MIT © 2026 — voir [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 suanlicat-bulk-apikey contributors

</div>