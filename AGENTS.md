# AGENTS.md

> ⚠️ **Leia este arquivo completo antes de qualquer ação — pesquisar, criar, editar, renomear, mover ou excluir arquivos.**

---

## Após qualquer operação

> ⚠️ **OBRIGATÓRIO:** após criar, editar, renomear, mover ou excluir **qualquer** arquivo:
>
> 1. Appendar entrada em `log.md` (ver formato abaixo)
> 2. Executar:
> ```bash
> git -C /root/wiki add -A
> git -C /root/wiki commit -m "<tipo>(<escopo>): <descrição>"
> git -C /root/wiki push origin main
> ```
> Nunca deixar o repositório com mudanças não commitadas ou não enviadas ao remoto.

**Commit message** — derivado diretamente da entrada do log: `tipo(escopo): descrição`  
**Tipos:** `edit`, `ingest`, `query`, `lint`, `session`, `chore`  
**Escopo:** nome da pasta ou arquivo principal afetado (ex: `vps`, `wiki`, `firecrawl`)

**Branches:** trabalhar em `main` por padrão. Feature branch só se explicitamente solicitado.

### Formato do log.md

`log.md` é append-only e cronológico. Nunca editar entradas existentes. **Sempre appendar no fim do arquivo** (`tail -5` retorna as mais recentes).

```
## [YYYY-MM-DD] <tipo> | <escopo> — <descrição>
- <detalhe relevante>
- <páginas tocadas, se aplicável>
```

| tipo | quando usar |
|---|---|
| `ingest` | nova fonte adicionada à wiki |
| `query` | pergunta gerou página nova arquivada |
| `lint` | health-check executado |
| `edit` | edição significativa em página existente |
| `session` | resumo de encerramento de sessão na wiki |
| `chore` | tarefa técnica sem mudança de conteúdo (trigger, teste, config) |

---

## O que é este repositório

**Hermes AI Memory Wiki** — base de conhecimento persistente do agente Hermes (assistente pessoal do Giovani). Segue o padrão **LLM Wiki de Karpathy**: Markdown puro como fonte da verdade, versionado por Git, indexado por SQLite + FTS5, visualizado no Obsidian.

**Princípio central:** conhecimento durável vai para a wiki. A `memory()` do Hermes (2.200 chars, volátil) é cache de sessão, não fonte da verdade.

---

## Autorização

Toda edição requer autorização explícita do usuário. A autorização vale só para o escopo pedido. Ao encontrar inconsistência: reportar, não corrigir — `"Encontrei [problema] em [arquivo]. Devo corrigir?"`

Arquivos que o agente NUNCA deve modificar: `.git/`, `.obsidian/`, `AGENTS.md`

---

## Verificação de estado

Estado atual de sistema (processo, serviço, cron, config, dado de aplicação) sempre se verifica ao vivo antes de qualquer ação — via `docker ps`, `systemctl status`, `crontab -l`, `ss -tlnp` ou API/arquivo do próprio sistema — nunca por dedução.

---

## Estrutura da wiki

Ver [[index.md]] — fonte de verdade da estrutura. Ao criar, renomear ou remover qualquer arquivo ou pasta, atualizar o `index.md`.

### Taxonomia de pastas

Cada pasta tem critério técnico específico. Ao criar um arquivo novo, aplicar os critérios abaixo para determinar onde ele vai. Se o conteúdo não se encaixar em nenhuma pasta existente, **não usar a mais próxima por nome** — reportar ao usuário e sugerir nova pasta com nome e critério definidos. A criação de novas pastas requer autorização explícita.

#### `systems/`
Entidades com runtime persistente e estado próprio entre sessões. Vai aqui quando o conteúdo descreve algo que: tem processo ou serviço ativo independente de invocação; é endereçável (API, porta, URL, identificador); tem ciclo de vida operacional (inicialização, shutdown, atualização, health); falhas nele propagam para outros componentes do setup.

Seções obrigatórias: `O que é` / `Stack e configuração` / `Interface` / `Operação` / `Erros conhecidos` / `Conexões`

#### `tools/`
Integrações externas invocadas explicitamente, sem runtime autônomo. Vai aqui quando o conteúdo descreve algo que: não mantém estado autônomo entre sessões; é chamado sob demanda por humano ou procedure; tem interface definida (API, CLI, MCP, plugin); provê capacidade delimitada e substituível.

Seções obrigatórias: `O que é` / `Capabilities` / `Limites` / `Como usar` / `Quando não usar` / `Configuração` / `Erros conhecidos` / `Status de validação` / `Conexões`

#### `procedures/`
Automações agênticas e workflows executáveis com entradas, saídas e gatilho definidos. Vai aqui quando o conteúdo descreve algo que: tem trigger explícito (manual, cron, hook, evento); tem sequência de passos reproduzível; produz output verificável; pode executar de forma autônoma ou semi-autônoma.

Seções obrigatórias: `O que faz` / `Gatilho` / `Como executar` / `Entradas e saídas` / `Arquivos` / `Resultado esperado` / `Conexões`

#### `concepts/`
Conhecimento abstrato não diretamente executável. Vai aqui quando o conteúdo descreve: princípios, padrões, decisões arquiteturais, planos, frameworks ou meta-conhecimento sobre o setup. Informa o PORQUÊ e o COMO das estruturas, não o QUE executar. Permanece relevante entre múltiplas sessões e contextos de uso.

Seções: livres, mas sempre com `Conexões` ao final.

#### `history/`
Registros imutáveis de eventos passados específicos e datados. Vai aqui quando o conteúdo documenta algo já encerrado: incidente, sessão significativa, migração, decisão com rationale. **Imutável após criação** — descreve o que aconteceu, não o que deve acontecer.

#### `diary/`
Staging automático gerado exclusivamente pelo plugin wiki-review. **Nunca criado ou editado manualmente.** Serve como inbox episódica de sessão; insights validados aqui devem ser migrados para páginas permanentes nas pastas acima.

#### `todo/`
Listas de itens acionáveis sem dono ou prazo definido. Items que amadurecem em workflows reproduzíveis devem ser promovidos para `procedures/`.

#### `raw/`
Fontes externas brutas. **Imutável** — nunca editar arquivos existentes, apenas adicionar.

---

## Frontmatter obrigatório (OKF)

Todo arquivo `.md` da wiki (exceto `AGENTS.md`, `index.md` e `log.md`) deve começar com:

```yaml
---
type: <tipo>
tags: [tag1, tag2]
title: <título>
description: <uma linha descrevendo o conteúdo>
timestamp: <ISO 8601>
status: <status>
---
```

**Tipos válidos:**

| type | uso |
|---|---|
| `system` | serviço ou sistema com runtime persistente (`systems/`) |
| `tool` | integração externa invocada explicitamente (`tools/`) |
| `concept` | conhecimento abstrato, framework, plano, meta-conhecimento (`concepts/`) |
| `procedure` | automação agêntica ou workflow executável (`procedures/`) |
| `session` | registro imutável de evento passado (`history/`) |
| `todo` | lista de pendências acionáveis (`todo/`) |
| `index` | ponto de entrada / mapa de navegação |
| `raw` | fonte bruta imutável (`raw/`) |
| `daily` | daily note episódica gerada pelo wiki-review (`diary/`) |

**Status válidos:**

| status | significado |
|---|---|
| `draft` | em construção, pode estar incompleto ou desatualizado — usar ao criar página nova |
| `stable` | confiável, revisado |
| `deprecated` | obsoleto, não usar como referência |

---

## Regras de escrita

1. **Uma página por conceito** — não duplicar. Se o conceito já existe, atualizar a página existente ou criar um link.
2. **Wikilinks** para conectar páginas relacionadas: `[[caminho/arquivo.md|texto]]`
3. **`raw/`** é imutável — arquivos ali nunca são editados, apenas adicionados
4. **Tags** em kebab-case, no plural, sem acentos (ex: `sessoes`, não `sessoe` ou `sessão`)

---

## Operações

### Ingest — adicionar arquivo ou conhecimento novo

Checklist obrigatório. Executar **nesta ordem** a cada novo arquivo criado:

1. **Determinar a pasta correta** usando a Taxonomia de pastas acima. Se o conteúdo não se encaixar em nenhuma pasta existente, **parar e reportar ao usuário** — sugerir nome e critério da nova pasta antes de criar qualquer arquivo.
2. **Criar o arquivo** no diretório determinado
3. **Adicionar frontmatter OKF completo** — `type`, `tags`, `title`, `description`, `timestamp`, `status`
4. **Adicionar wikilinks** para páginas relacionadas (e atualizar as páginas relacionadas para linkar de volta)
5. **Atualizar `index.md`** — nova entrada com link + descrição na seção correta; árvore sincronizada com `git ls-files`

### Query — responder a uma pergunta com base na wiki

1. Ler `index.md` para identificar páginas relevantes
2. Ler as páginas identificadas
3. Sintetizar resposta com referências (`[[página]]`)
4. Se a resposta for valiosa (análise, comparação, decisão), **criar uma página nova** com ela — bons insights não devem ficar só no chat

### Lint — health-check periódico

Executar quando solicitado pelo usuário:

- Páginas na pasta errada segundo os critérios da Taxonomia de pastas acima
- Páginas com `type` inconsistente com a pasta em que estão
- Páginas órfãs sem nenhum link apontando pra elas
- Contradições entre páginas (`status: stable` conflitando com info mais recente)
- Conceitos mencionados em várias páginas mas sem página própria
- Entradas no `index.md` sem correspondente em `git ls-files` (e vice-versa)
