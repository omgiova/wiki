---
type: raw
tags: [n8n, trello, workflow-json]
title: JSON do workflow "Trello Prazos por Membro - Open Mídia" (v1 oficial, 2026-07-09)
description: Export completo (nodes, connections, settings) do Fluxo 2 no estado em que foi validado e colocado em produção — primeira versão oficial
timestamp: 2026-07-09T00:00:00-03:00
status: stable
---

Export via API REST do n8n (`GET /api/v1/workflows/PRaGCXrFKcmiusfE`) em 2026-07-09, já em produção, com números reais dos 4 membros e `errorWorkflow` apontado para o `Alerta de Erro`. Ver [[wiki/projects/automacao-trello-open-midia.md|Automação Trello — Open Mídia]] para a documentação legível deste fluxo.

```json
{
  "id": "PRaGCXrFKcmiusfE",
  "name": "Trello Prazos por Membro - Open Mídia",
  "active": true,
  "nodes": [
    {
      "parameters": {
        "url": "https://api.trello.com/1/boards/6908bffbc7473c1134fe279d/cards",
        "authentication": "predefinedCredentialType",
        "nodeCredentialType": "trelloApi",
        "sendQuery": true,
        "queryParameters": {
          "parameters": [
            {
              "name": "fields",
              "value": "name,due,shortUrl,idList,idMembers"
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "id": "4f8b7ac9-2733-4ed6-bc04-ba94081c26b0",
      "name": "Buscar cards do board",
      "position": [448, 0],
      "credentials": {
        "trelloApi": {
          "id": "EoFpfQ3mK6YAbg90",
          "name": "Trello account"
        }
      }
    },
    {
      "parameters": {
        "jsCode": "const listMap = {\n  '6908c0021d3d2a7086b24430': { name: 'Informações gerais', pos: 16384 },\n  '695cf5a70f935a1eff02f577': { name: 'OPEN MÍDIA', pos: 24576 },\n  '6984b4d0a73553b1402d0838': { name: 'LIBERTAS', pos: 82944 },\n  '6908c05e151fff2df047675b': { name: 'ANDERSON NAZARIO', pos: 84480 },\n  '6908c016b25e940124c49ca7': { name: 'PATI BONETTI', pos: 180224 },\n  '69d815fb27afd92243833be8': { name: 'TEACHER THAIS', pos: 185344 },\n  '69cc36b17a53a898a587d89b': { name: 'JOBS EXTRAS', pos: 186368 },\n  '69f8eb8dc065c37ed1913354': { name: 'Domain', pos: 187392 },\n  '6984b4c4a417fa675612ad54': { name: 'ARQUIVADO', pos: 188416 },\n};\n\n// Janela rolante em horário de Brasília (UTC-3 fixo, sem horário de verão desde 2019).\n// Usar o relógio local do container (pode ser UTC) para \"hoje\" causava bug: cards do dia\n// anterior em BRT apareciam na janela. Aqui a data de hoje é lida via Intl na tz de SP e\n// o limite é montado como instante UTC absoluto (não depende da tz do servidor).\nconst now = new Date();\nconst todayStr = new Intl.DateTimeFormat('en-CA', { timeZone: 'America/Sao_Paulo' }).format(now); // YYYY-MM-DD\nconst start = new Date(`${todayStr}T00:00:00-03:00`);\nconst windowDays = 7;\nconst end = new Date(start.getTime() + (windowDays + 1) * 24 * 60 * 60 * 1000 - 1);\n\nconst members = {\n  '69e148a7bc59b79cf77d3dcc': 'Giovani',\n  '5fca48c6b1e8557a10d4da11': 'Gabriele',\n  '55612c7040fe932af37bec26': 'Luciana',\n  '5f46e3482f13ae46ee5f0d96': 'Nathalia',\n};\nconst nicknames = {\n  '69e148a7bc59b79cf77d3dcc': 'Gio',\n  '5fca48c6b1e8557a10d4da11': 'Gabi',\n  '55612c7040fe932af37bec26': 'Lu',\n  '5f46e3482f13ae46ee5f0d96': 'Nathalia',\n};\n\nconst grouped = {};\n\nfor (const item of $input.all()) {\n  const card = item.json;\n  if (!card.due) continue;\n  const due = new Date(card.due);\n  if (due < start || due > end) continue;\n  const memberIds = card.idMembers || [];\n  for (const mid of memberIds) {\n    if (!members[mid]) continue;\n    if (!grouped[mid]) grouped[mid] = {};\n    const listId = card.idList;\n    if (!grouped[mid][listId]) grouped[mid][listId] = [];\n    grouped[mid][listId].push({ name: card.name, due, shortUrl: card.shortUrl });\n  }\n}\n\nconst fmtShort = d => d.toLocaleDateString('pt-BR', { timeZone: 'America/Sao_Paulo', day: '2-digit', month: '2-digit' });\nconst fmtFull = d => d.toLocaleDateString('pt-BR', { timeZone: 'America/Sao_Paulo' });\nconst header = `📋 Aqui estão seus prazos dessa semana (de ${fmtShort(start)} a ${fmtShort(end)}):`;\n\nconst output = [];\nfor (const [mid, byList] of Object.entries(grouped)) {\n  const listIds = Object.keys(byList).sort((a, b) => (listMap[a]?.pos || 0) - (listMap[b]?.pos || 0));\n  const blocks = listIds.map(listId => {\n    const cards = byList[listId].sort((a, b) => a.due - b.due);\n    const listName = listMap[listId]?.name || '(sem lista)';\n    const cardLines = cards.map(c => `📌 ${c.name}\\n🗓️ Vence em *${fmtFull(c.due)}*\\n🔗 ${c.shortUrl}`);\n    return `*${listName}*\\n\\n${cardLines.join('\\n\\n')}`;\n  });\n  const greeting = `Oi, ${nicknames[mid]}!`;\n  const message = `${greeting}\\n\\n${header}\\n\\n${blocks.join('\\n\\n')}`;\n  output.push({ json: { memberId: mid, memberName: members[mid], message } });\n}\n\nreturn output;"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "id": "cd120254-3840-4f64-90bc-93a588515f9e",
      "name": "Filtrar e agrupar por membro",
      "position": [672, 0]
    },
    {
      "parameters": {
        "rules": {
          "values": [
            {
              "conditions": {
                "options": {
                  "caseSensitive": true,
                  "leftValue": "",
                  "typeValidation": "strict",
                  "version": 2
                },
                "conditions": [
                  {
                    "leftValue": "={{ $json.memberId }}",
                    "rightValue": "69e148a7bc59b79cf77d3dcc",
                    "operator": { "type": "string", "operation": "equals" },
                    "id": "e050b8b9-d9b3-4f8d-aeb2-ee961dda8a27"
                  }
                ],
                "combinator": "and"
              },
              "renameOutput": true,
              "outputKey": "Giovani"
            },
            {
              "conditions": {
                "options": {
                  "caseSensitive": true,
                  "leftValue": "",
                  "typeValidation": "strict",
                  "version": 2
                },
                "conditions": [
                  {
                    "leftValue": "={{ $json.memberId }}",
                    "rightValue": "5fca48c6b1e8557a10d4da11",
                    "operator": { "type": "string", "operation": "equals" },
                    "id": "30a617f7-813b-4725-816e-706483ac9b90"
                  }
                ],
                "combinator": "and"
              },
              "renameOutput": true,
              "outputKey": "Gabriele"
            },
            {
              "conditions": {
                "options": {
                  "caseSensitive": true,
                  "leftValue": "",
                  "typeValidation": "strict",
                  "version": 2
                },
                "conditions": [
                  {
                    "leftValue": "={{ $json.memberId }}",
                    "rightValue": "55612c7040fe932af37bec26",
                    "operator": { "type": "string", "operation": "equals" },
                    "id": "7ebe8652-11ad-4095-b99a-fd2f14dabc74"
                  }
                ],
                "combinator": "and"
              },
              "renameOutput": true,
              "outputKey": "Luciana"
            },
            {
              "conditions": {
                "options": {
                  "caseSensitive": true,
                  "leftValue": "",
                  "typeValidation": "strict",
                  "version": 2
                },
                "conditions": [
                  {
                    "leftValue": "={{ $json.memberId }}",
                    "rightValue": "5f46e3482f13ae46ee5f0d96",
                    "operator": { "type": "string", "operation": "equals" },
                    "id": "44a6bd39-3b20-412a-85c2-5abeafa74a7c"
                  }
                ],
                "combinator": "and"
              },
              "renameOutput": true,
              "outputKey": "Nathalia"
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.switch",
      "typeVersion": 3.2,
      "id": "3119e94b-dea0-4e20-bc01-e39caad22850",
      "name": "Por membro",
      "position": [896, 0]
    },
    {
      "parameters": {
        "resource": "messages-api",
        "instanceName": "Giobot",
        "remoteJid": "5511986501499@s.whatsapp.net",
        "messageText": "={{ $json.message }}",
        "options_message": {}
      },
      "type": "n8n-nodes-evolution-api.evolutionApi",
      "typeVersion": 1,
      "id": "ed61ace1-069d-46bd-a64e-410618d7fc59",
      "name": "Enviar texto - Giovani",
      "position": [1152, -288],
      "credentials": {
        "evolutionApi": { "id": "kCB0BkGSnS34Tdhx", "name": "Evolution account" }
      }
    },
    {
      "parameters": {
        "resource": "messages-api",
        "instanceName": "Giobot",
        "remoteJid": "5511944007603@s.whatsapp.net",
        "messageText": "={{ $json.message }}",
        "options_message": {}
      },
      "type": "n8n-nodes-evolution-api.evolutionApi",
      "typeVersion": 1,
      "id": "ceb33796-d060-482f-8dd5-c664d6748d8a",
      "name": "Enviar texto - Gabriele",
      "position": [1152, -96],
      "credentials": {
        "evolutionApi": { "id": "kCB0BkGSnS34Tdhx", "name": "Evolution account" }
      }
    },
    {
      "parameters": {
        "resource": "messages-api",
        "instanceName": "Giobot",
        "remoteJid": "5511988389199@s.whatsapp.net",
        "messageText": "={{ $json.message }}",
        "options_message": {}
      },
      "type": "n8n-nodes-evolution-api.evolutionApi",
      "typeVersion": 1,
      "id": "810775d2-a173-4766-95e0-9cc01d3ae883",
      "name": "Enviar texto - Luciana",
      "position": [1152, 96],
      "credentials": {
        "evolutionApi": { "id": "kCB0BkGSnS34Tdhx", "name": "Evolution account" }
      }
    },
    {
      "parameters": {
        "resource": "messages-api",
        "instanceName": "Giobot",
        "remoteJid": "5511971803910@s.whatsapp.net",
        "messageText": "={{ $json.message }}",
        "options_message": {}
      },
      "type": "n8n-nodes-evolution-api.evolutionApi",
      "typeVersion": 1,
      "id": "ce03e4a2-f522-4b8a-850c-765fe7e93f38",
      "name": "Enviar texto - Nathalia",
      "position": [1152, 288],
      "credentials": {
        "evolutionApi": { "id": "kCB0BkGSnS34Tdhx", "name": "Evolution account" }
      }
    },
    {
      "parameters": {
        "rule": { "interval": [{ "field": "weeks", "triggerAtDay": [1], "triggerAtHour": 10 }] }
      },
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1.2,
      "id": "0799ca38-f712-47d7-a009-7aa85f897b0b",
      "name": "Schedule (Segunda 10h)",
      "position": [80, 16]
    },
    {
      "parameters": {
        "rule": { "interval": [{ "field": "weeks", "triggerAtDay": [2], "triggerAtHour": 20, "triggerAtMinute": 44 }] }
      },
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1.2,
      "id": "489476eb-a3e6-4e28-b8d1-1940185bfa5a",
      "name": "Schedule (Segunda 10h)1",
      "position": [160, -160]
    },
    {
      "parameters": {},
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [80, 240],
      "id": "b6a818ff-1de5-4b5d-8fa1-dbd9be49cdfb",
      "name": "When clicking ‘Execute workflow’"
    }
  ],
  "connections": {
    "Buscar cards do board": {
      "main": [[{ "node": "Filtrar e agrupar por membro", "type": "main", "index": 0 }]]
    },
    "Filtrar e agrupar por membro": {
      "main": [[{ "node": "Por membro", "type": "main", "index": 0 }]]
    },
    "Por membro": {
      "main": [
        [{ "node": "Enviar texto - Giovani", "type": "main", "index": 0 }],
        [{ "node": "Enviar texto - Gabriele", "type": "main", "index": 0 }],
        [{ "node": "Enviar texto - Luciana", "type": "main", "index": 0 }],
        [{ "node": "Enviar texto - Nathalia", "type": "main", "index": 0 }]
      ]
    },
    "Schedule (Segunda 10h)": {
      "main": [[{ "node": "Buscar cards do board", "type": "main", "index": 0 }]]
    },
    "Schedule (Segunda 10h)1": { "main": [[]] },
    "When clicking ‘Execute workflow’": { "main": [[]] }
  },
  "settings": {
    "executionOrder": "v1",
    "binaryMode": "separate",
    "timeSavedMode": "fixed",
    "errorWorkflow": "3MI1k15YL5OUrEXF",
    "callerPolicy": "workflowsFromSameOwner",
    "availableInMCP": false
  }
}
```
