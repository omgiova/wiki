---
type: tool
tags: [hermes, desktop, electron, gateway, conexao-remota, autenticacao, cookies]
title: Hermes Desktop — Conexão com Gateway Remoto
description: Como o app Hermes Desktop (Windows) conecta ao Hermes Agent da VPS, e como reconectar quando a sessão cai
timestamp: 2026-08-11T00:15:00-03:00
status: stable
---

# Hermes Desktop — Conexão com Gateway Remoto

---

## O que é

Shell desktop nativo (Electron) do Hermes Agent, feito pela Nous Research. Roda no PC Windows e se conecta a um backend Hermes — local (instalado na própria máquina) ou **remoto** (o [[wiki/systems/hermes.md|Hermes]] da [[wiki/systems/vps.md|VPS]]).

| Item | Valor |
|---|---|
| Nome/versão do app | `hermes` / **0.15.1** (`productName: Hermes`) |
| Runtime | Electron, `main: electron/main.cjs` |
| Executável | `%LOCALAPPDATA%\hermes\hermes-agent\apps\desktop\release\win-unpacked\Hermes.exe` |
| Código empacotado | `resources/app.asar` (extrair com `npx @electron/asar extract`) |
| Pasta de dados (userData) | `%APPDATA%\Hermes\` |
| Gateway remoto em uso | `http://<IP-DA-VPS>:9119` — Hermes Agent **0.19.0** |

> O app do PC (0.15.1) é mais antigo que o backend da VPS (0.19.0). A conexão funciona mesmo assim.

---

## Capabilities

- Conectar a um gateway Hermes remoto por HTTP/HTTPS (`normalizeRemoteBaseUrl` rejeita qualquer outro protocolo)
- Dois modos de autenticação, detectados automaticamente via `GET /api/status`:
  - **`token`** — token estático; REST usa header `X-Hermes-Session-Token`, WS usa `?token=`
  - **`oauth`** — sessão por cookie HttpOnly; WS exige ticket de uso único de `POST /api/auth/ws-ticket`. Ativado quando `/api/status` retorna `auth_required: true`
- Override total por variável de ambiente, ignorando config e UI: `HERMES_DESKTOP_REMOTE_URL` + `HERMES_DESKTOP_REMOTE_TOKEN` (só funciona no modo `token`)

---

## Limites

- **Sem OAuth por env var** — o override por ambiente só cobre modo `token`
- **Cookie não vai no `connection.json`** — em modo `oauth` a credencial vive na partição de sessão do Electron, não em arquivo de config legível
- **Login nativo do app é por janela** — `openOauthLoginWindow` abre um BrowserWindow e espera o cookie aparecer; sem interação, não há login pela UI
- **O app não renova sozinho** — o provider `basic` do gateway emite access token sem refresh automático confiável; quando expira, é preciso logar de novo

---

## Como usar

### Reconexão rápida (caminho normal)

Fechar o Hermes e dar **duplo clique no atalho "Reconectar Hermes"** na Área de Trabalho.

Ele faz login no gateway e injeta os cookies sozinho — sem digitar nada, sem abrir tela.

| Item | Caminho |
|---|---|
| Atalho | `%USERPROFILE%\Desktop\Reconectar Hermes.lnk` |
| Script | `%LOCALAPPDATA%\hermes\reconnect\reconnect.cjs` + `reconnect.bat` |
| Credenciais | hardcoded no `reconnect.cjs` (arquivo local, fora do git) |

O `.bat` aborta se o `Hermes.exe` estiver aberto — a partição de cookies fica travada com o app rodando.

**O que o script faz, nesta ordem:**

1. `POST /auth/password-login` com usuário e senha → recebe `Set-Cookie`
2. Limpa cookies antigos da partição (importante se o `secret` do servidor mudou)
3. Grava os 3 cookies em `persist:hermes-remote-oauth` + `flushStore()`
4. Escreve `connection.json` com `mode: remote`

### Reconexão manual (se o script sumir)

```bash
# 1. Login — devolve os cookies
curl -i -X POST http://<IP-DA-VPS>:9119/auth/password-login \
  -H "Content-Type: application/json" \
  -d '{"provider":"basic","username":"<user>","password":"<senha>","next":""}'
```

Depois, com o Hermes fechado, rodar um script Electron que grave os cookies na partição
`persist:hermes-remote-oauth` — usando o Electron já presente na máquina:

```
%LOCALAPPDATA%\hermes\hermes-agent\node_modules\electron\dist\electron.exe <script.cjs>
```

> O script **precisa** fazer `app.setName('Hermes')` e `app.setPath('userData', <appData>/Hermes)`.
> Sem isso o Electron resolve o userData como `Electron` e grava os cookies na pasta errada — silenciosamente.

### Verificar se está tudo certo

```bash
# Gateway no ar + modo de auth
curl -s http://<IP-DA-VPS>:9119/api/status

# Validade do cookie que o servidor emite agora
curl -si -X POST http://<IP-DA-VPS>:9119/auth/password-login \
  -H "Content-Type: application/json" \
  -d '{"provider":"basic","username":"<user>","password":"<senha>","next":""}' \
  | grep -i "set-cookie: hermes_session_at"

# O token autentica de verdade? (endpoint protegido)
curl -s -X POST -H "Cookie: hermes_session_at=<TOKEN>" \
  http://<IP-DA-VPS>:9119/api/auth/ws-ticket
```

`/api/status` e `/api/auth/providers` são **públicos** — respondem 200 sem credencial. Nunca use os
dois para testar autenticação. O teste real é `/api/auth/ws-ticket`: sem cookie válido retorna
`401 {"reason":"no_cookie"}`.

---

## Quando não usar

- **Não editar `connection.json` esperando autenticar** — em modo `oauth` ele só guarda URL e modo; a credencial está na partição de cookies. Mexer só no JSON dá "conectado" sem sessão.
- **Não usar `HERMES_DESKTOP_REMOTE_TOKEN`** com este gateway — ele está em modo cookie; o header `X-Hermes-Session-Token` é rejeitado (confirmado: 401 em endpoint protegido).
- **Não rodar o script com o app aberto** — a partição fica travada e a gravação se perde.

---

## Configuração

### `%APPDATA%\Hermes\connection.json`

```json
{
  "mode": "remote",
  "remote": {
    "url": "http://<IP-DA-VPS>:9119",
    "authMode": "oauth"
  }
}
```

`mode` aceita `local` ou `remote` (qualquer outro valor vira `local`). `authMode` aceita `oauth` ou
`token` (default `token`). O app relê o arquivo quando o `mtime` muda, então edição externa é detectada.

### Cookies — partição `persist:hermes-remote-oauth`

| Cookie | Papel | Max-Age atual |
|---|---|---|
| `hermes_session_at` | access token (o que autentica) | 31536000 (1 ano) |
| `hermes_session_rt` | refresh token | 2592000 (30 dias) |
| `hermes_session_provider` | `basic` | 2592000 |

Gravados com `httpOnly: true`, `path: /`, `sameSite: 'lax'`, `secure: false` (gateway em HTTP puro).

### Lado servidor (VPS)

O que controla a duração da sessão, no `config.yaml` do **perfil que o dashboard usa**:

```yaml
dashboard:
  basic_auth:
    username: <user>
    password_hash: "scrypt$..."
    secret: "<32+ bytes aleatorios>"
    session_ttl_seconds: 31536000
```

Override por ambiente, com prioridade sobre o YAML de **qualquer** perfil:

```
HERMES_DASHBOARD_BASIC_AUTH_TTL_SECONDS=31536000
```

- **`secret` fixo** → a sessão sobrevive a reinício do servidor. Sem ele, o segredo é regerado a cada boot e todos os cookies morrem.
- **`session_ttl_seconds`** → prazo do token. Default 43200 (12h). Sem teto documentado.
- Os dois são necessários: TTL longo com `secret` volátil ainda cai a cada restart.

---

## Erros conhecidos

### "Conectou e caiu em ~12h"

Access token com TTL padrão de 43200s. Corrigir na VPS (`session_ttl_seconds` ou a env var) e **logar de novo** — o prazo fica gravado dentro do token já emitido, então mudar a config não estende o que já existe.

### Config alterada na VPS não faz efeito (o caso que custou horas — 2026-08-11)

Sintoma: `config.yaml` com `session_ttl_seconds: 31536000`, ocorrência única, sem override no `.env`, gateway reiniciado — e o login continuava emitindo `Max-Age=43200`.

Três causas possíveis, todas reais e difíceis de distinguir:

1. **Perfil errado.** Perfis são isolados por `HERMES_HOME`; cada um tem seu próprio `config.yaml` em `~/.hermes/profiles/<nome>/config.yaml`. O `~/.hermes/config.yaml` é só o perfil `default`. Editar o arquivo errado não gera erro nenhum. Esta VPS roda `gateway_mode: multiple` com 3 perfis (`default`, `gio`, `gio2`).
   ```bash
   grep -rn "session_ttl_seconds" ~/.hermes/          # acha todas as ocorrências
   tr '\0' '\n' < /proc/$(pgrep -f "hermes dashboard" | head -1)/environ | grep HERMES_HOME
   ```
2. **Reiniciou o componente errado.** `/api/status` mostra `gateway` e `dashboard` como componentes **separados**. Quem emite o cookie é o **dashboard**. Reiniciar só o gateway não recarrega a config de auth.
3. **Env var sobrescrevendo.** `HERMES_DASHBOARD_BASIC_AUTH_TTL_SECONDS` vence o YAML.

> **Lição:** nunca deduzir que a config pegou. Verificar sempre no `Max-Age` de um login real — é a única evidência que vale.

### "Não acho onde conectar no app"

A opção de conexão remota existe (confirmado no código), mas some depois que o app já instalou um backend local. Não vale caçar botão: o caminho por arquivo (`connection.json` + cookies) funciona sempre e independe da tela.

### Cookies pararam de valer sem expirar

O `secret` do servidor mudou. Todo cookie assinado com o segredo antigo vira inválido na hora. Rodar o atalho de reconexão — ele limpa os antigos antes de gravar os novos.

---

## Status de validação

✅ **Validado ponta a ponta em 2026-08-11.**

- `Max-Age=31536000` confirmado na resposta do servidor; token expira em **2027-08-11**
- Token autenticado em endpoint protegido (`/api/auth/ws-ticket` → 200 com ticket)
- Cookie relido da partição do app com validade `2027-08-11` — gravação confirmada
- `connection.json` em `mode: remote`
- App abriu conectado à VPS

**Pendências:**

- ⚠️ Confirmar se `HERMES_DASHBOARD_BASIC_AUTH_TTL_SECONDS` ficou persistido no systemd/script de boot da VPS. Se foi só no comando manual, o próximo restart volta pros 12h.
- ⚠️ **Segurança:** token de 1 ano trafegando em HTTP puro (`--insecure`) numa porta pública. Quem interceptar tem acesso por um ano. O certo é Tailscale ou HTTPS — aí o TTL longo deixa de ser risco.
- ❓ A doc oficial afirma que o provider `basic` não tem refresh token, mas o gateway 0.19.0 **emite** `hermes_session_rt` com 30 dias. Não testado se o refresh funciona de fato.

---

## Conexões

- [[wiki/systems/hermes.md|Hermes]] — o backend na VPS ao qual este app se conecta
- [[wiki/systems/hermes-endpoints.md|Hermes API]] — referência dos endpoints REST
- [[wiki/systems/vps.md|VPS]] — host do gateway
- [[wiki/tools/telegram.md|Telegram]] — outra interface do mesmo gateway
