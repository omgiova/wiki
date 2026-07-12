---
type: raw
tags: [n8n, trello, github, workflow-json]
title: JSON do workflow "Trello Open Mídia - Banco de Dados" (v1 validada, 2026-07-12)
description: Export completo (nodes, connections, settings) do Fluxo 3 no estado validado pelo Giovani — v1 IMUTÁVEL; automações futuras devem duplicar este flow, nunca editar o original
timestamp: 2026-07-12T16:05:43-03:00
status: stable
---

Export via API REST do n8n (`GET /api/v1/workflows/VPIpLm5pujpZvVDY`) em 2026-07-12, imediatamente após a validação do Giovani nos testes de card único com gravação real no repo GitHub `omgiova/om-database`. Ver [[wiki/projects/automacao-trello-open-midia.md|Automação Trello — Open Mídia]] para a documentação legível deste fluxo.

> ⚠️ **Regra definida pelo Giovani em 2026-07-12:** este workflow validado NÃO será mais editado. Qualquer automação derivada (ex.: versão board inteiro) deve **duplicar** o flow no n8n e editar a cópia.

```json
{
  "id": "VPIpLm5pujpZvVDY",
  "name": "Trello Open Mídia - Banco de Dados",
  "active": false,
  "nodes": [
    {
      "parameters": {},
      "id": "2fd2a97d-49d1-41cd-9789-a9cfa9139491",
      "name": "Ao clicar em Executar",
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [
        0,
        0
      ]
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "c6bcd958-abeb-40bc-9afb-fe6f27867ebb",
              "name": "cardUrl",
              "value": "https://trello.com/c/mm5Slwyt",
              "type": "string"
            }
          ]
        },
        "options": {}
      },
      "id": "9d740433-b2e0-454f-a58f-5cd8bbe423da",
      "name": "Card de teste (colar URL aqui)",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        224,
        0
      ]
    },
    {
      "parameters": {
        "url": "=https://api.trello.com/1/cards/{{ $json.cardUrl.includes(\"/c/\") ? $json.cardUrl.split(\"/c/\")[1].split(\"/\")[0] : $json.cardUrl.trim() }}",
        "authentication": "predefinedCredentialType",
        "nodeCredentialType": "trelloApi",
        "sendQuery": true,
        "queryParameters": {
          "parameters": [
            {
              "name": "fields",
              "value": "name,desc,due,closed,idList,shortLink,shortUrl,dateLastActivity,labels"
            },
            {
              "name": "list",
              "value": "true"
            },
            {
              "name": "list_fields",
              "value": "name"
            },
            {
              "name": "members",
              "value": "true"
            },
            {
              "name": "member_fields",
              "value": "fullName,username"
            },
            {
              "name": "checklists",
              "value": "all"
            },
            {
              "name": "attachments",
              "value": "true"
            },
            {
              "name": "actions",
              "value": "commentCard"
            },
            {
              "name": "actions_limit",
              "value": "1000"
            }
          ]
        },
        "options": {}
      },
      "id": "dd7d29e8-d4e2-48e0-8610-addd2a341f7e",
      "name": "Buscar card completo",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [
        448,
        0
      ],
      "credentials": {
        "trelloApi": {
          "id": "EoFpfQ3mK6YAbg90",
          "name": "Trello account"
        }
      }
    },
    {
      "parameters": {
        "jsCode": "// ===== MAPAS FIXOS (blocos seguros de editar) =====\nconst FORMATOS = {\n  carrossel: 'carrossel', c: 'carrossel',\n  estatico: 'estatico',\n  reels: 'reels', r: 'reels', rt: 'reels', 'reels-teste': 'reels', video: 'reels',\n  storie: 'storie', stories: 'storie',\n};\nconst CANAIS = { linkedin: 'linkedin', instagram: 'instagram' };\nconst LISTA_EXCECOES = { '6984b4d0a73553b1402d0838': 'LIBERTAS' };\nconst LISTAS_IGNORADAS = ['6908c0021d3d2a7086b24430']; // Informações gerais\n// ==================================================\n\nfunction kebab(s) {\n  return String(s).normalize('NFD').replace(/[̀-ͯ]/g, '')\n    .toLowerCase().replace(/[^a-z0-9]+/g, '-').replace(/^-+|-+$/g, '');\n}\nfunction brt() {\n  return new Date(Date.now() - 3 * 3600 * 1000).toISOString().replace('Z', '-03:00');\n}\nfunction dataBR(d) {\n  return new Date(d).toLocaleDateString('pt-BR', { timeZone: 'America/Sao_Paulo' });\n}\n\nconst out = [];\nfor (const item of items) {\n  const card = item.json;\n\n  // organizadores de semana e lista Informações gerais não entram\n  const nome = String(card.name);\n  if (LISTAS_IGNORADAS.includes(card.idList) || /^\\s*📅/.test(nome) || /SEMANA\\s*\\d/i.test(nome)) {\n    out.push({ json: { ignorado: true, motivo: 'organizador de semana ou lista Informações gerais', card: nome } });\n    continue;\n  }\n\n  // lista -> type (mesma limpeza do Fluxo 2)\n  let cliente = (card.list?.name || '').replace(/^\\d+\\s*-\\s*/, '').split('|')[0].trim();\n  if (LISTA_EXCECOES[card.idList]) cliente = LISTA_EXCECOES[card.idList];\n  const type = kebab(cliente) || 'sem-lista';\n\n  // nome do card: tokens por \"|\" — data, formato e canal detectados; o resto vira assunto\n  // regra de conflito: nome do card vence etiqueta\n  const tokens = nome.split('|').map(t => t.trim()).filter(Boolean);\n  let dataNome = null, formato = null;\n  const canalSet = new Set();\n  const assuntoParts = [];\n  for (const t of tokens) {\n    const k = kebab(t);\n    if (!formato && FORMATOS[k]) { formato = FORMATOS[k]; continue; }\n    if (CANAIS[k]) { canalSet.add(CANAIS[k]); continue; }\n    if (!dataNome && /^\\d{1,2}[\\/.\\-]\\d{1,2}([\\/.\\-]\\d{2,4})?$/.test(t)) { dataNome = t; continue; }\n    assuntoParts.push(t);\n  }\n  const assunto = assuntoParts.join(' | ') || nome;\n\n  // etiquetas: formato só como fallback, canal soma, resto vira tag; sem nome = ignorada\n  const labels = (card.labels || []).filter(l => l.name && l.name.trim());\n  if (!formato) {\n    const lf = labels.find(l => FORMATOS[kebab(l.name)]);\n    if (lf) formato = FORMATOS[kebab(lf.name)];\n  }\n  for (const l of labels) { if (CANAIS[kebab(l.name)]) canalSet.add(CANAIS[kebab(l.name)]); }\n  const canal = [...canalSet];\n  const etiquetas = labels.map(l => l.name.trim());\n\n  const membros = (card.members || []).map(m => m.fullName);\n\n  const fm = [\n    '---',\n    `type: ${type}`,\n    'tags: []',\n    `etiquetas: [${etiquetas.map(e => '\"' + e.replace(/\"/g, '') + '\"').join(', ')}]`,\n    `title: \"${nome.replace(/\"/g, '\\\\\"')}\"`,\n    'description: \"\"',\n    `timestamp: ${brt()}`,\n    `formato: ${formato || ''}`,\n    `canal: [${canal.join(', ')}]`,\n    `lista: \"${(card.list?.name || '').replace(/\"/g, '\\\\\"')}\"`,\n    `membros: [${membros.join(', ')}]`,\n    `criado: ${dataBR(parseInt(card.id.substring(0, 8), 16) * 1000)}`,\n    `trello_id: ${card.id}`,\n    `url: ${card.shortUrl}`,\n    'status: draft',\n    '---',\n  ];\n\n  const SEM_DADOS = '_[sem dados]_';\n  const body = ['', `# ${assunto}`, '', '## Descrição', ''];\n  body.push(card.desc && card.desc.trim() ? card.desc.trim() : SEM_DADOS);\n\n  body.push('', '## Checklists');\n  const checklists = (card.checklists || []).slice().sort((a, b) => a.pos - b.pos);\n  if (checklists.length) {\n    for (const cl of checklists) {\n      body.push('', `### ${cl.name}`, '');\n      for (const ci of (cl.checkItems || []).slice().sort((a, b) => a.pos - b.pos)) {\n        body.push(`- [${ci.state === 'complete' ? 'x' : ' '}] ${ci.name}`);\n      }\n    }\n  } else {\n    body.push('', SEM_DADOS);\n  }\n\n  body.push('', '## Comentários', '');\n  const comentarios = (card.actions || []).filter(a => a.type === 'commentCard').reverse();\n  if (comentarios.length) {\n    for (const c of comentarios) {\n      body.push(`- **${c.memberCreator?.fullName || '?'}** (${dataBR(c.date)}): ${String(c.data?.text || '').trim()}`);\n    }\n  } else {\n    body.push(SEM_DADOS);\n  }\n\n  body.push('', '## Anexos', '');\n  const anexos = card.attachments || [];\n  if (anexos.length) {\n    for (const a of anexos) body.push(`- [${a.name}](${a.url})`);\n  } else {\n    body.push(SEM_DADOS);\n  }\n\n  const markdown = fm.join('\\n') + body.join('\\n') + '\\n';\n  const filename = [type, formato, kebab(assunto), card.shortLink].filter(Boolean).join('-') + '.md';\n  out.push({ json: { filename, markdown } });\n}\nreturn out;"
      },
      "id": "8805c4e3-7956-4c35-90c3-9de5022d2f30",
      "name": "Montar markdown (OKF)",
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [
        672,
        0
      ]
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "loose",
            "version": 2
          },
          "combinator": "and",
          "conditions": [
            {
              "id": "95cf4210-0bfb-4f7a-9b3a-325b8df50546",
              "leftValue": "={{ $json.filename }}",
              "rightValue": "",
              "operator": {
                "type": "string",
                "operation": "exists",
                "singleValue": true
              }
            }
          ]
        },
        "options": {}
      },
      "id": "714576c3-8bf8-4668-b639-35593930445e",
      "name": "Só cards válidos",
      "type": "n8n-nodes-base.filter",
      "typeVersion": 2.2,
      "position": [
        880,
        0
      ]
    },
    {
      "parameters": {
        "resource": "file",
        "owner": {
          "__rl": true,
          "mode": "list",
          "value": "omgiova",
          "cachedResultName": "omgiova",
          "url": "https://github.com/omgiova"
        },
        "repository": {
          "__rl": true,
          "mode": "list",
          "value": "om-database",
          "cachedResultName": "om-database",
          "url": "https://github.com/omgiova/om-database"
        },
        "filePath": "={{ $json.filename }}",
        "fileContent": "={{ $json.markdown }}",
        "commitMessage": "=card: {{ $json.filename }}"
      },
      "id": "79be2ca1-f3d2-4d5c-a68a-f7847bc6cbd2",
      "name": "Gravar no GitHub (novo)",
      "type": "n8n-nodes-base.github",
      "typeVersion": 1.1,
      "position": [
        1104,
        0
      ],
      "webhookId": "c9ae3730-1728-4d0a-8473-a7839f2e5186",
      "credentials": {
        "githubApi": {
          "id": "LPJ3zUEPsCXyHJPg",
          "name": "GitHub account"
        }
      },
      "onError": "continueErrorOutput"
    },
    {
      "parameters": {
        "resource": "file",
        "operation": "edit",
        "owner": {
          "__rl": true,
          "mode": "list",
          "value": "omgiova",
          "cachedResultName": "omgiova",
          "url": "https://github.com/omgiova"
        },
        "repository": {
          "__rl": true,
          "mode": "list",
          "value": "om-database",
          "cachedResultName": "om-database",
          "url": "https://github.com/omgiova/om-database"
        },
        "filePath": "={{ $json.filename }}",
        "fileContent": "={{ $json.markdown }}",
        "commitMessage": "=card: {{ $json.filename }}"
      },
      "id": "d0276a41-a1a1-41c1-bb45-17381ee707c1",
      "name": "Atualizar no GitHub (já existe)",
      "type": "n8n-nodes-base.github",
      "typeVersion": 1.1,
      "position": [
        1328,
        128
      ],
      "webhookId": "e8aca339-f822-4810-a8ce-8fbfc08bbd65",
      "credentials": {
        "githubApi": {
          "id": "LPJ3zUEPsCXyHJPg",
          "name": "GitHub account"
        }
      }
    }
  ],
  "connections": {
    "Ao clicar em Executar": {
      "main": [
        [
          {
            "node": "Card de teste (colar URL aqui)",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Card de teste (colar URL aqui)": {
      "main": [
        [
          {
            "node": "Buscar card completo",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Buscar card completo": {
      "main": [
        [
          {
            "node": "Montar markdown (OKF)",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Montar markdown (OKF)": {
      "main": [
        [
          {
            "node": "Só cards válidos",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Só cards válidos": {
      "main": [
        [
          {
            "node": "Gravar no GitHub (novo)",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Gravar no GitHub (novo)": {
      "main": [
        [],
        [
          {
            "node": "Atualizar no GitHub (já existe)",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "settings": {
    "executionOrder": "v1",
    "binaryMode": "separate",
    "availableInMCP": false
  }
}
```
