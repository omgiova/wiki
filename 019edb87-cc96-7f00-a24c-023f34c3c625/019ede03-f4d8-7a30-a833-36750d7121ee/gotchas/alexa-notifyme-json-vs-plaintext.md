---
tags:
- alexa
- gotcha
- node-red
- notifyme
tier: semantic
---
# Gotcha: Alexa NotifyMe só aceita text/plain, não JSON

## Sintomas
- POST com `Content-Type: application/json` no endpoint `/lembretes` do Node-RED
- Node-RED loga: `[warn] [alexa-notifyme] JSON and Buffers not allowed`
- A notificação nunca chega na Alexa

## Causa
O nó `alexa-notifyme` no Node-RED espera `msg.payload` como **string**. Se recebe um objeto JSON, rejeita com warning e não envia.

## Comando correto
```bash
curl -X POST "http://localhost:8800/lembretes" \
  -H "Content-Type: text/plain" \
  -d "mensagem para a Alexa"
```

## Referência
- Skill: `alexa-notifications`
- Node-RED container: `projetos_nodered` (porta interna 8800, mapeada pra 1880)
- API externa: `https://api.notifymyecho.com/v1/NotifyMe`
