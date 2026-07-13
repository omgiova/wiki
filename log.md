# log.md

Registro cronológico de operações na wiki. Append-only — nunca editar entradas existentes.

---

## [2026-06-28] edit | AGENTS.md — reestruturação e adição do log.md
- Removida linha de compatibilidade do cabeçalho
- Fundidas seções Autorização e Arquivos NUNCA modificar
- Removidas regras de escrita duplicatas (3 e 7)
- Adicionada instrução de leitura completa antes de qualquer ação
- Incorporado log.md como etapa obrigatória dentro da seção Git e commits

## [2026-06-28] edit | proximos-passos — pendência urgente: gráfico Android com nós fantasma
- Adicionado item 0 (urgente): gráfico Android mostra viz.html/README do raw/ como nós fantasma
- PC exibe corretamente; Android não — diferença provavelmente em graph.json (gitignored)

## [2026-06-28] edit | obsidian-git — removido wikilink quebrado para 2026-06-24-obsidian-git-setup
- Seção Conexões tinha link para arquivo que nunca existiu

## [2026-06-28] edit | wikilinks quebrados — removidos de index.md, crise-update.md, 2026-06-24
- index.md: removidos 5 links para diários deletados da seção diary/ e da árvore
- crise-update.md: convertido wikilink para 2026-06-20.md em texto plano
- 2026-06-24-20260624.md: convertidos 2 wikilinks para agent-loop-architectures.md em texto plano

## [2026-06-28] edit | obsidian-git — nós fantasma no gráfico por wikilinks quebrados
- Documentado: gráfico mostra nós fantasma por wikilinks quebrados, não por arquivos no filesystem
- Erro histórico #11: teoria do usuário sobre links quebrados era correta — foi descartada errado
- Lista de notas com wikilinks quebrados identificada (9 arquivos)
- Duas opções de fix documentadas: ocultar no gráfico ou corrigir os links

## [2026-06-28] edit | obsidian-git — limitações git clean no Android e checklist de limpeza
- Documentado: git clean -fd não remove diretórios não rastreados de forma confiável no FAT (Android)
- Adicionado checklist completo de limpeza manual para Android
- Nota sobre necessidade de force-close do Obsidian após limpeza pelo Termux

## [2026-06-28] edit | obsidian-git — Android FAT filesystem e core.hooksPath documentados
- Android: `storage/shared` é FAT/exFAT, não suporta chmod +x — hook deve ir em `~/git-hooks/` (ext4)
- Solução: `core.hooksPath` no git config aponta para armazenamento interno do Termux
- Erros históricos #9 e #10 adicionados
- Status de instalação atualizado: Windows ✅, Android validação pendente

## [2026-06-28] edit | obsidian-git — solução definitiva post-merge hook documentada
- Adicionada seção "Solução definitiva para arquivos fantasma" com post-merge hook
- Comandos de instalação para Windows e Android
- Tabela de status de instalação por device (ambos pendentes)

## [2026-06-28] edit | obsidian-git — lista de comandos confirmada e erro documentado
- Adicionada lista completa de comandos disponíveis na paleta (Android, confirmada via screenshot)
- Documentado: não existe "Sync Method: Reset" nem equivalente a `git clean` na interface do plugin
- Adicionada seção "Arquivos fantasma" com causa raiz e fixes (Termux ou deleção manual)
- Adicionado erro #8 em "Erros históricos do agente": afirmação falsa sobre "Sync Method: Reset"

## [2026-06-28] edit | wiki-review — comparação com background_review incorporada
- Conteúdo de wiki-review-vs-background-review.md movido para wiki-review.md como seção "Comparação completa com background_review (41 itens)"
- wiki-review-vs-background-review.md deletado
- index.md: entrada removida da árvore de diretórios

## [2026-06-28] edit | wiki-review — origem como clone do background_review documentada no topo
- Adicionado parágrafo de abertura explicando que wiki_review nasceu como clone do background_review.py

## [2026-06-28] edit | AGENTS.md — tipos de commit unificados com log e formato do log corrigido
- Tipos de commit: `docs`, `fix`, `feat` substituídos por `edit`, `ingest`, `query`, `lint`, `session`, `chore`
- Formato do log: `<título>` → `<escopo> — <descrição>` para derivação mecânica do commit message
- Adicionado `chore` na tabela de tipos do log

## [2026-06-28] edit | AGENTS.md — regra de derivação do commit message explicitada
- Bloco Tipos/Escopo reescrito com "Commit message — derivado diretamente da entrada do log: tipo(escopo): descrição"

## [2026-06-28] edit | concepts/plano-implementacao-loop — Curador da Wiki movido para ✅ Validado
- Seção "🚧 A validar" removida (único item validado)
- Nova subsection "Curador da Wiki — v1" em "✅ Validado" com arquitetura real, decisões de design e arquivos da v1
- Frontmatter: timestamp atualizado para 2026-06-28, status draft → stable

## [2026-06-28] edit | concepts/plano-implementacao-loop — Curador da Wiki: separado "automação validada" de "loop validado"
- Restaurada seção "🚧 A validar (como loop)" com framing correto
- Curador v1 descrito como automação funcionando, mas comportamento como loop (cron, idempotência, cobertura) ainda não testado

## [2026-06-28] edit | tools/telegram — consolidação de 4 arquivos em página única
- Criado wiki/tools/telegram.md com todo o conteúdo migrado literalmente
- Deletados: telegram-bot-api.md, telegram-topicos.md, telegram-send-rich-message.md, telegram-reacoes.md
- Atualizado index.md: 4 entradas → 1 (árvore e lista)
- Atualizado curador-wiki.md: links de conexões apontam para telegram.md
- Seções: Chats e IDs / HERMES_SESSION_MESSAGE_ID / Envio de Mensagens / Reações / Integrações

## [2026-06-28] edit | AGENTS.md — append do log sempre no fim do arquivo
- Explicitado que entradas mais recentes vão no final (tail -5 = mais recentes)

## [2026-06-28] edit | AGENTS.md — taxonomia de pastas e correções de conflitos
- Adicionada seção "Taxonomia de pastas" com critérios técnicos e seções obrigatórias por pasta
- Tipos OKF expandidos: adicionados `system` e `tool`; mapeados para pastas correspondentes
- Removida regra 4 de Regras de escrita (diary/ — específico de procedure, não de AGENTS)
- Checklist de Ingest: item 1 reescrito para obrigar verificação de taxonomia antes de criar arquivo
- Lint: adicionadas verificações de pasta errada e type inconsistente com pasta

## [2026-06-28] chore | wiki — refatoração completa da taxonomia de pastas
- Renomeadas/criadas: infraestrutura/ → systems/ e tools/, automacao/ → procedures/ e tools/, conhecimento/ → concepts/, historico/ → history/, pendencias/ → todo/, diario/ → diary/
- Todos os arquivos movidos com git mv (histórico preservado)
- Todos os wikilinks atualizados em todos os arquivos
- index.md: árvore, seções e entradas sincronizadas com nova estrutura
- AGENTS.md: referências a pastas atualizadas (ingest checklist + regra diary/)
- curador-wiki.md e curador-wiki-historico.md: prompts ativos atualizados com novos nomes de pastas
- Referências em history/ e orquestrador.md preservadas como registro histórico

## [2026-06-28] chore | wiki — remoção das pastas antigas após refatoração de taxonomia
- Deletadas: automacao/, conhecimento/, diario/, historico/, infraestrutura/, pendencias/
- .gitkeep movido de diario/ para diary/ (git mv)
- wiki/ agora contém apenas as 7 pastas da nova taxonomia: concepts/, diary/, history/, procedures/, systems/, todo/, tools/

## [2026-06-28] ingest | procedures/auditor-wiki — documentação do auditor v1
- Criado wiki/procedures/auditor-wiki.md: documenta arquitetura 3 fases, escopo dos agentes, 9 pontos de validação
- Criados /root/auditor-wiki-agent-prompt-v1.md e /root/auditor-wiki-coord-prompt-v1.md
- Criado /root/auditor-wiki-v1.sh: script principal bash
- index.md atualizado: entrada adicionada em procedures/

## [2026-06-28] edit | systems/hermes — reestruturação para type: system com seções obrigatórias
- hermes.md: type concept → system; adicionadas seções O que é, Stack e configuração, Interface, Operação, Erros conhecidos, Conexões
- hermes-api.md: type concept → system; description atualizada para refletir papel como seção Interface do sistema
- index.md: descrição de hermes.md atualizada

## [2026-06-28] edit | systems/hermes-api — renomear para hermes-endpoints.md
- hermes-api.md renomeado para hermes-endpoints.md (nome mais preciso — só endpoints REST, não toda a interface)
- Links atualizados em: index.md, hermes.md, elevenlabs-mcp.md, telegram.md, orquestrador.md
- log.md preservado (append-only, entradas históricas mantidas)

## [2026-06-28] edit | procedures/auditor-wiki — reescrita completa para arquitetura v2
- auditor-wiki-v1.sh: reescrito — descoberta dinâmica de arquivos, agentes por pasta, overlap, links, validação Telegram com inline keyboard, correções por finding com commit imediato
- auditor-wiki-agent-prompt-v1.md: adicionados checks 7 (qualidade do nome) e 8 (labels de wikilinks)
- auditor-wiki-coord-prompt-v1.md: output alterado de markdown para JSON (executive_summary + findings com correctable)
- auditor-wiki-corrector-prompt-v1.md: novo — gera old_string/new_string por finding
- auditor-wiki-overlap-prompt-v1.md: novo — detecta sobreposição semântica cross-folder
- auditor-wiki-links-prompt-v1.md: novo — valida wikilinks quebrados e labels
- procedures/auditor-wiki.md: documentação atualizada para refletir arquitetura completa

## [2026-06-28] edit | procedures/auditor-wiki — corrigir numeração de fases (0→1-based) e sugestões de nomes de prompt
- Fases renumeradas: 0–5 → 1–6 no script e na documentação
- auditor-wiki.md atualizado para refletir nova numeração

## [2026-06-28] chore | auditor-wiki — renomear prompts para padrão prompt-awv1-<papel>
- auditor-wiki-agent-prompt-v1.md → prompt-awv1-pasta.md
- auditor-wiki-coord-prompt-v1.md → prompt-awv1-coordenador.md
- auditor-wiki-corrector-prompt-v1.md → prompt-awv1-corretor.md
- auditor-wiki-overlap-prompt-v1.md → prompt-awv1-overlap.md
- auditor-wiki-links-prompt-v1.md → prompt-awv1-links.md
- auditor-wiki-v1.sh e auditor-wiki.md atualizados com novos caminhos

## [2026-06-28] edit | procedures/auditor-wiki.md — adiciona seção de validações pré-produção (V1–V17)
- Restauradas 15 validações perdidas em sessão anterior (V1–V15)
- Corrigida referência "Fase 0" → "Fase 1" em V1
- Corrigido "5 fases" → "6 fases" no parágrafo de abertura
- Adicionadas V16 (autenticação claude CLI standalone) e V17 (dois findings no mesmo arquivo)

## [2026-06-28] edit | procedures/auditor-wiki.md — V9: documentado script de teste inline keyboard
- Expandido item V9 com descrição do script /root/test-v9-inline-keyboard.sh
- Script criado: drena offset, sendMessage com reply_markup, poll callback_query, answerCallbackQuery, editMessageReplyMarkup
- Resultado esperado documentado: status ok, answer_ok True, botões removidos após clique

## [2026-06-28] edit | procedures/auditor-wiki.md — protocolo obrigatório de execução de validações
- Adicionado bloco de aviso com os 4 passos obrigatórios: criar → documentar → commitar → rodar
- Regra explícita: nunca rodar sem documentar antes; autorização do usuário vem após ver a documentação commitada

## [2026-06-28] edit | procedures/auditor-wiki.md — V18: poll_text com filtro de prefixo "!" documentado
- Contexto do conflito Hermes vs auditor documentado (mesmo token, long-polling sem webhook)
- Script criado: /root/test-v18-poll-text-prefix.sh — duas fases + verificação manual do Hermes
- Solução proposta: prefixo "!" nas respostas ao auditor; strip antes de usar como new_string

## [2026-06-28] edit | procedures/auditor-wiki.md — V9 marcado como validado (✅)

## [2026-06-28] edit | procedures/auditor-wiki.md — V18: resultado real documentado (falhou verificação manual)
- Prefixo "!" não protege: Hermes recebeu e respondeu à mensagem, gastando token
- Ambos (script e Hermes) consumiram o mesmo update simultaneamente
- V18 NÃO validado; abordagem de prefixo simples descartada

## [2026-06-28] edit | procedures/auditor-wiki.md — V18b: prefixo "/" documentado como alternativa ao "!"
- Hipótese: "/" trata mensagens como comando no Hermes, sem acionar LLM
- Script criado: /root/test-v18b-slash-prefix.sh
- Verificação manual: observar se Hermes aciona LLM ou só responde "desconhecido"

## [2026-06-28] edit | procedures/auditor-wiki.md — V9 desmarcado (parcial); D1 e D2 documentados
- V9: voltou para ⚠️ — testado com 2 botões, script real envia 3
- D1: fluxo "Ajustar" inviável — usuário não sabe new_string de cabeça; proposta: dropdown com opções válidas + "Outro"
- D2: V9 precisa ser refeito para 3 botões (findings corrigíveis) e 2 botões (não-corrigíveis)

## [2026-06-28] edit | procedures/auditor-wiki.md — V9 consolidado (7 interações); V18/V18b colapsados
- V9 reescrito com script único test-v9-completo.sh cobrindo todas as 7 interações
- V18/V18b colapsados em nota histórica (prefixo "/" validado, incorporado ao V9)
- D2 fechado (coberto pelo V9 completo)

## [2026-06-28] edit | procedures/auditor-wiki.md — V9 reestruturado em V9a/V9b/V9c; fix Recomeçar documentado
- V9 dividido em 3 scripts por tipo de mensagem (resumo, corrigível, não-corrigível)
- Botão Recomeçar (restart/exit 2/exec) adicionado ao Resumo executivo
- poll_text com filtro "/" incorporado ao script real e aos testes
- D1 e D2 atualizados

## [2026-06-28] chore | scripts — renomeados de test-vN para valid-N-papel
- Padrão antigo "test-v9" parecia número de versão; novo padrão: valid-<validação>-<papel>
- valid-9-resumo.sh, valid-9-corrigivel.sh, valid-9-nao-corrigivel.sh
- valid-18-prefixo-exclamacao.sh, valid-18-prefixo-barra.sh (históricos)
- Wiki atualizada com novos caminhos

## [2026-06-28] edit | procedures/auditor-wiki.md — V9a validado (3/3)

## [2026-06-28] edit | procedures/auditor-wiki.md — V9b validado (3/3)

## [2026-06-28] edit | procedures/auditor-wiki.md — V9c validado (2/2); validação 9 completa
- V9c: finding não-corrigível, 2/2 passaram
- Validação 9 inteira concluída: V9a 3/3, V9b 3/3, V9c 2/2
- Fix aplicado: botão Recomeçar, poll_text com filtro /, prompts atualizados

## [2026-06-28] edit | procedures — removida V18 de auditor-wiki.md
- V18 era resquício redundante do V9 (já documentado e validado como V9a/V9b/V9c)

## [2026-06-28] query | auditor-wiki — documentado primeiro run real (desastre)
- Todos os 8 agentes produziram prosa em vez de JSON — findings = []
- Coordenador recebeu input vazio e falhou com parse error
- Mensagem Telegram enviada sem conteúdo útil
- Gastou ~75% do limite Claude free em ~5 minutos (normalmente leva horas para consumir isso)
- Adicionada seção "Primeira execução real — 2026-06-28 (desastre)" com timeline, falhas, consumo, análise de causa raiz e lições
- Nenhuma correção aplicada — só documentação

## [2026-06-28] query | auditor-wiki — análise de consumo de tokens (causa raiz do desastre)
- Adicionada seção "Análise do consumo de tokens" com decomposição do gasto por agente
- Identificados 3 fatores principais: 8 agentes paralelos × multi-turn Read × contexto acumulativo
- Cada agente faz 2-5 Read calls (AGENTS.md + arquivos da pasta), contexto cresce a cada turno
- struct_str (~10KB) enviado a TODOS os agentes mesmo os que só precisam de 1 arquivo
- Overlap/link agents podem ler centenas de KB se ativarem Read em múltiplos arquivos
- Comparação: uso normal = ~5-15 requests/h sequencial; auditor = ~35-50 requests em 5 min paralelo
- Total estimado: ~700KB-1,5MB tokens de entrada + ~40-160KB saída em 8 sessões simultâneas
- Claude Code free tier foi desenhado para uso sequencial, não para 8 sessões paralelas em rajada

## [2026-06-28] edit | procedures/auditor-wiki — corrige suposição de "free tier" para Claude Pro
- 5 ocorrências de "free tier" / "Claude free" removidas do auditor-wiki.md
- Substituídas por linguagem factual: "cota do Claude Code CLI", "consumo em rajada", sem especular sobre plano
- O usuário é assinante Claude Pro — informação incorreta documentada no log histórico (não editado, append-only)

## [2026-06-28] edit | procedures/auditor-wiki — restaura dado factual de 75% que foi removido por engano
- O 75% é dado observado pelo usuário, não suposição de plano — foi restaurado como "~75% da cota disponível"
- O erro anterior foi associar o percentual a "free tier" em vez de mantê-lo como fato

## [2026-06-28] ingest | raw — libertas.md: guia de voz Libertas Assessoria Financeira
- Adicionado frontmatter OKF (type: raw, tags, title, description)
- index.md: entrada adicionada na árvore e na seção raw/raiz
- Páginas tocadas: raw/libertas.md, index.md

## [2026-06-28] chore | log — correção: frontmatter em raw revertido
- A entrada anterior registrava adição de frontmatter em raw/libertas.md — isso foi revertido (raw/ é imutável)
- raw/libertas.md está exatamente como o usuário adicionou: sem frontmatter
- index.md: entrada raw/libertas.md na árvore e na seção raw/raiz (válida — link no index, não edição do raw)
- Páginas tocadas: log.md (esta correção)

## [2026-06-28] session | libertas — revisão carrossel com 7 Sweeps (conversion-copywriting)
- Duplicado v2 → v3 em /root/Libertas/carrossel-financeiro-investimento-v3.md
- Aplicado framework 7 Sweeps do conversion-copywriting: 10 intervenções identificadas
- Insumos: raw/libertas.md (guia de voz) + briefing do próprio carrossel
- Nada aplicado ao v3 — documentado para referência futura
- Criado wiki/history/2026-06-28-libertas-carrossel-7sweeps.md
- Páginas tocadas: history/2026-06-28-libertas-carrossel-7sweeps.md, index.md, log.md

## [2026-06-29] ingest | raw — akshay-pachaar-karpathy-agentic-engineering-tooling.md
- Adicionado artigo de Akshay Pachaar publicado em 2026-06-28
- Título: "Karpathy's Agentic Engineering Finally Has Proper Tooling"
- Fonte: blog.dailydoseofds.com (cross-post do artigo no X)
- Conteúdo: Google Agents CLI — scaffolding, eval e deploy de agentes ADK em ambiente unificado
- Inclui 7 imagens com URLs originais do Substack
- Páginas tocadas: raw/akshay-pachaar-karpathy-agentic-engineering-tooling.md

## [2026-06-29] ingest | raw — agents-cli-README.md
- Adicionado README oficial do repositório google/agents-cli (182 linhas)
- Fonte: https://github.com/google/agents-cli/blob/main/README.md
- Páginas tocadas: raw/agents-cli-README.md

## [2026-06-29] chore | raw — rename google-okf/README.md → google-okf-README.md
- Renomeado para padronizar nomenclatura de READMEs na pasta raw/
- Páginas tocadas: raw/google-okf-README.md

## [2026-06-29] chore | index — corrigir entradas raw/ não atualizadas
- Corrigido link google-okf/README.md → google-okf-README.md
- Adicionado raw/akshay-pachaar-karpathy-agentic-engineering-tooling.md
- Adicionado raw/agents-cli-README.md
- Páginas tocadas: index.md

## [2026-06-29] chore | index — corrigir árvore e seções raw/ e diario/
- Árvore atualizada: removido google-okf/README.md, adicionados 3 arquivos raw/ na raiz
- Seção raw/: google-okf-README.md movido para ### raiz (estava incorretamente em ### google-okf/)
- Seção diary/ renomeada para diario/ com 2 entradas de 2026-06-28 adicionadas
- Páginas tocadas: index.md

## [2026-06-29] edit | todo — adicionar item prioridade altíssima: corrigir wiki_review pasta diario/
- Adicionado item 0 no topo das pendências: wiki_review grava em diary/ (errado) em vez de diario/
- Páginas tocadas: wiki/todo/proximos-passos.md

## [2026-06-29] chore | raw — reverter: google-okf-README.md volta para raw/google-okf/
- Arquivo movido indevidamente para raw/ raiz; revertido para raw/google-okf/google-okf-README.md
- Index atualizado: árvore e seção google-okf/ corrigidas
- Páginas tocadas: raw/google-okf/google-okf-README.md, index.md

## [2026-06-29] ingest | procedures — criar auditor-wiki-evals.md
- Novo arquivo: wiki/procedures/auditor-wiki-evals.md
- Diagnóstico da 1ª execução (desastre 2026-06-28) + lições dos raws agents-cli e Karpathy
- Gates 0–8 substituindo validações opcionais V1–V17 por sequência bloqueante obrigatória
- Mapeamento V1–V17 → gates, princípios de redesign, consideração de arquitetura alternativa
- Páginas tocadas: wiki/procedures/auditor-wiki-evals.md, index.md

## [2026-06-29] edit | procedures — rename Gate → Eval em auditor-wiki-evals.md
- Renomeado "Gate" para "Eval" em todos os títulos e referências
- "## Gates de validação" → "## Evals de validação"
- Gates 1–10 → Evals 1–10; referências internas atualizadas
- Páginas tocadas: wiki/procedures/auditor-wiki-evals.md

## [2026-06-29] edit | procedures — complemento Eval 4 com mecanismo de polling
- Eval 4: esclarecido que "aguardar callback" requer polling ativo (curl getUpdates com sleep)
- Claude Code não suspende sessão passivamente; critério de aprovação atualizado
- Páginas tocadas: wiki/procedures/auditor-wiki-evals.md

## [2026-06-29] edit | procedures — regra obrigatória + resultado Eval 1
- Adicionado aviso no topo do arquivo: documentar resultado antes de qualquer próxima ação
- Eval 1 marcado como aprovado (2026-06-29): /root/.claude/agents/auditor-pasta.md criado e verificado
- Páginas tocadas: wiki/procedures/auditor-wiki-evals.md

- **2026-06-29T08:07:02-03:00** | edit(auditor-wiki-evals) | reescrita da seção de princípios e evals: adicionado princípio 7 (sintético antes de real), campo _meta definido, soft timeout (10 calls), evals reestruturados de 10 para 12 com 2 novos (Eval 3: _meta validation; Eval 6: rampa de dados reais), critérios de reprovação explícitos em todos os evals com tokens

- **2026-06-29T08:12:03-03:00** | ingest(concepts) | criado automacao-principios.md — documento geral de princípios de automação e testes: 8 princípios (sintético antes de real, fail-fast, serial antes de paralelo, prompt-as-contract, custo como critério, _meta, soft timeout, orquestrador não confia no silêncio), ciclo de maturidade, mecanismo JSONL, postmortem v1 mapeado para princípios; adicionado ao index.md

- **2026-06-29T08:13:34-03:00** | edit(concepts/procedures) | adicionados links faltantes nas Conexões: automacao-principios.md agora aponta para os raws (Karpathy + agents-cli); auditor-wiki-evals.md agora aponta para automacao-principios.md

## [2026-06-29] edit | auditor-wiki-evals — Eval 2 dividido em 2-A e 2-B
- Eval 2-A: smoke test mínimo (prompt "nenhum arquivo, retorne findings vazio") — sem lógica de auditoria, sem problema plantado
- Eval 2-B: primeiro teste de lógica (conteúdo inline com campo status ausente)
- Adicionada seção "Como capturar estatísticas de tokens" com metodologia JSONL delta
- Campo _meta passa a ser obrigatório a partir do Eval 2-A
- Timestamp: 2026-06-29T08:33:19-03:00

## [2026-06-29] ingest | todo/violacoes-agentes — criado registro de violações graves de agentes
- V1 documentado: edição de entrada existente no log.md (regra append-only quebrada em 2026-06-29)
- Timestamp: 2026-06-29T08:35:41-03:00

## [2026-06-29] edit | auditor-wiki-evals — Eval 2-A: breakdown de tokens documentado honestamente
- Adicionada tabela com origem real dos tokens: system prompt do subagente (~950) é o custo dominante, não o prompt enviado (~20)
- Documentado comportamento de cache: primeira invocação = cache_creation (custo cheio), subsequentes em 5min = cache_read (~10x mais barato)
- Estatísticas a registrar expandidas para incluir cache_creation_input_tokens e cache_read_input_tokens separadamente
- Timestamp: 2026-06-29T08:40:00-03:00

## [2026-06-29] edit | auditor-wiki-evals — Eval 2 reestruturado em 2-A, 2-B e 2-C
- 2-A: agente genérico sem system prompt, prompt mínimo "retorne {ok: true}" — testa só o mecanismo, ~30 tokens
- 2-B: auditor-pasta com findings vazio — primeiro contato com o agente nomeado, ~1.025 tokens
- 2-C: auditor-pasta com problema plantado inline — primeiro teste de lógica de auditoria
- Breakdown de tokens documentado por subeval com distinção cache_creation vs cache_read
- Timestamp: 2026-06-29T08:50:00-03:00

## [2026-06-29] edit | auditor-wiki-evals — Eval 2-A executado e aprovado; estimativas corrigidas
- Eval 2-A: ✅ APROVADO — {"ok": true} válido, sem prosa, tool_uses 0, duração 1.245s
- subagent_tokens real: 12.603 (estimativa era ~30 — erro de 400×)
- Causa: system prompt interno do Claude Code (~12K tokens) não estava na estimativa
- Estimativas do 2-B corrigidas: ~13.025 tokens (era ~1.025)
- Adicionada regra: modelo deve ser declarado explicitamente e exibido no resultado de cada eval
- Modelo do 2-A: herdou da sessão pai (claude-sonnet-4-6) — não foi declarado explicitamente
- Timestamp: 2026-06-29T09:16:44-03:00

## [2026-06-29] ingest | /root/eval-2a-runner.md — arquivo runner autocontido para Eval 2-A
- Criado em /root/ (fora da wiki) para uso em sessão limpa (/clear)
- Autocontido: instrui Claude a não ler wiki nem outros arquivos
- Inclui: spawn com model=sonnet explícito, validação via Bash, template de relatório
- Métrica: subagent_tokens da notificação (não JSONL delta — lição do run anterior)
- Timestamp: 2026-06-29T09:26:30-03:00

## [2026-06-29] edit | wiki/procedures — Eval 2-A: 2ª execução documentada
- Critérios marcados como [x] (todos aprovados)
- Stats da 2ª execução adicionados em tabela: subagent_tokens 12.603, input 3, cache_creation 12.599, cache_read 0, output 1, duration 1.807ms
- Modelo explicitamente declarado via model: "sonnet" — lição da 1ª execução cumprida
- Arquivo: wiki/procedures/auditor-wiki-evals.md

## [2026-06-29] edit | wiki/procedures — Eval 2-A: contexto de invocação da 2ª execução registrado
- Adicionado: /clear antes do eval, prompt exato enviado, tabela comparativa entre 1ª e 2ª execuções
- Lições: /clear elimina cache_read e reduz input_tokens; overhead base (~12.6K) domina sempre
- Arquivo: wiki/procedures/auditor-wiki-evals.md

## [2026-06-29] ingest | procedures — Eval 2-B: runner criado e execução documentada
- Runner criado: /root/eval-2b-runner.md (autocontido, mesmo padrão do 2-A)
- Wiki: seção Eval 2-B atualizada com instrução de execução (/clear + prompt exato), resposta esperada e estimativas de tokens corrigidas pós-2A
- Arquivo tocado: wiki/procedures/auditor-wiki-evals.md

## [2026-06-29] edit | procedures — Eval 2-B: prompt corrigido para proibir Read calls explicitamente
- Prompt anterior era ambíguo — agente poderia ler AGENTS.md antes de perceber que não havia arquivos
- Correção: "Não faça Read calls" adicionado explicitamente ao prompt do subagente
- Arquivos: /root/eval-2b-runner.md e wiki/procedures/auditor-wiki-evals.md

## [2026-06-29] eval | procedures — Eval 2-B: 3ª execução (v3) — REPROVADO
- Resultado do subagente correto (JSON válido, findings:[], read_calls:0), mas runner reprovado
- Causa 1: Passo 3 (bash JSONL) gerou turno extra + tool calls → cache_read inflado (57K real)
- Causa 2: JSONL tem 3 registros por chamada API — soma de todos triplicou os números (122K reportado)
- Decisão: runner reescrito para v4 — sem bash, sem Python, validação inline, zero tool calls no turno 2
- Arquivo: wiki/procedures/auditor-wiki-evals.md

## [2026-06-29] edit | runner — Eval 2-B runner reescrito para v4
- Removidos: Passo 3 (extração JSONL) e Passo 4 (Python bash) inteiramente
- Turno 2 passa a ter zero tool calls: validação feita inline a partir da notificação
- Adicionado critério formal: subagent_tokens < 15.000
- Histórico de versões atualizado no arquivo
- Arquivo: /root/eval-2b-runner.md

## [2026-06-29] config | sistema — hook log-guard configurado
- Hook PreToolUse adicionado em ~/.claude/settings.json (escopo global)
- Script: /root/.claude/hooks/log-guard.py
- Comportamento: bloqueia Edit/Write em log.md que modifique conteúdo existente; permite apenas append
- Motivação: reincidência de V1 (edição de entrada existente no log.md)
- Validação pendente: verificar nas próximas sessões se o bloqueio funciona corretamente

## [2026-06-29] edit | todo — violacoes-agentes.md atualizado
- V1 consolidado: duas ocorrências da mesma violação (edição de log.md) + correção aplicada (hook) + checklist de validação pendente
- V2 registrado: não atualizar log.md após editar a wiki (ocorrência 1: esta sessão)
- Arquivos: wiki/todo/violacoes-agentes.md

## [2026-06-29] edit | evals — Eval 2-B aprovado (4ª execução — v4)
- Runner v4: validação inline, zero tool calls no turno 2, subagent_tokens < 15.000
- subagent_tokens: 12.830, tool_uses: 0, duration_ms: 2.427, JSON válido ✅
- Arquivo: wiki/procedures/auditor-wiki-evals.md

## [2026-06-29] edit | evals — Regra de transparência de custos adicionada; 4ª execução invalidada
- Nova regra obrigatória: todo eval deve reportar tokens do subagente E da sessão pai
- 4ª execução do Eval 2-B marcada como INVÁLIDO (runner v4 omitiu tokens do pai)
- Arquivo: wiki/procedures/auditor-wiki-evals.md

## [2026-06-29] edit | runner — Eval 2-B runner reescrito para v5
- v5: turno 2 inline sem tool calls + turno 3 separado com bash/Python para tokens do pai
- Deduplicação correta: primeiro de cada grupo de 3 registros idênticos, última entrada excluída
- Passo 4 agora é critério obrigatório de aprovação (tokens do pai não podem ser omitidos)
- Arquivo: /root/eval-2b-runner.md
- evals.md atualizado: 5ª execução marcada como pendente

## [2026-06-29] edit | procedures — Eval 2-B 5ª execução: ✅ APROVADO (v5)
- Runner v5 executado: terminal fechado/reaberto → /clear → prompt
- Subagente genérico (model: sonnet) com system prompt inline
- JSON válido, findings: [], read_calls == 0, sem prosa, tool_uses: 0
- subagent_tokens: 12.830 (< 15.000 ✅)
- Tokens do pai (JSONL deduplificado): input=4, cache_creation=3.042, cache_read=41.850, output=791
- Passo 3 (turno 2): zero tool calls ✅ | Passo 4 (turno 3): bash/Python executado ✅
- Todos os critérios de aprovação satisfeitos
- auditor-wiki-evals.md atualizado com resultado completo

## [2026-06-29] edit | procedures — corrige terminologia de cabeçalhos de execução nos evals
- Substituído "✅ APROVADO / ❌ REPROVADO / ❌ INVÁLIDO" nos cabeçalhos por "realizada" ou "realizada - INVÁLIDA"
- STATUS (aprovado/reprovado) permanece dentro do bloco de resultado — cabeçalho só indica que a execução ocorreu
- Afeta: Eval 1, Eval 2-A (2 execuções), Eval 2-B (5 execuções)

## [2026-06-29] edit | procedures — adiciona ✅ APROVADA nos evals 1 e 2-A
- Eval 1: execução marcada como "realizada — ✅ APROVADA"
- Eval 2-A: ambas as execuções (1ª e 2ª) marcadas como "realizada — ✅ APROVADA"
- Evals aprovados agora distinguíveis visualmente dos realizados sem aprovação

## [2026-06-29] edit | procedures — corrige STATUS da 5ª execução e adiciona questão em aberto
- STATUS da 5ª execução do Eval 2-B: "✅ APROVADO" → "realizado" (critérios do runner satisfeitos, otimização de tokens do pai em aberto)
- Adicionada questão em aberto: testar variante 2 turnos (Passos 3+4 mesclados) para descobrir o cache_read mínimo possível

## [2026-06-29] edit | procedures — encerra 2-B, redefine 2-C para arquitetura sem subagentes
- Eval 2-B: marcado como ENCERRADO por mudança de arquitetura (subagentes descartados)
- v4 do Eval 2-B: reforçada invalidação total — ⛔ não citar, não usar como referência
- Eval 2-C: completamente reescrito para nova arquitetura sem subagentes
- Arquivo sintético criado: /tmp/eval-2c-test.md (status ausente, problema plantado)
- Prompt exato para próxima sessão documentado no próprio 2-C

## [2026-06-29] edit | procedures — documenta resultado do Eval 2-C (✅ APROVADA)
- Eval 2-C: 1ª execução realizada e aprovada — arquitetura sem subagentes funciona
- Campo `status` detectado como ausente, severidade `critico` reportada, tamanho 231 chars informado
- Checkboxes de critérios atualizados ([ ] → [x]) no arquivo de evals
- Limitação registrada: tokens do pai não capturados (sem runner dedicado nesta execução)

## [2026-06-29] edit | procedures — corrige Eval 2-C para incompleto; adiciona regra de status pelo usuário
- 1ª execução do Eval 2-C: status alterado de "APROVADA" para "INCOMPLETA" (tokens do pai não capturados)
- Checkboxes revertidos para [ ] — status não confirmado pelo Giovani
- Adicionada regra obrigatória no topo: status de eval é definido PELO USUÁRIO, nunca pelo agente

## [2026-06-29] edit | procedures — cria runner 2-C e atualiza prompt/como-executar no evals
- Criado /root/eval-2c-runner.md: 3 passos, custo isolado por turno via JSONL
- Eval 2-C no evals: referência ao runner adicionada, prompt simplificado, nota de status pelo usuário

## [2026-06-29] chore | procedures — remove runner 2-C; restaura prompt direto no evals
- Runner /root/eval-2c-runner.md deletado (padrão 2-B aplicado errado em arquitetura sem subagentes)
- Evals: referência ao runner removida, prompt direto original restaurado

## [2026-06-29] edit | procedures — corrige critérios Eval 2-C: rastreabilidade obrigatória, prompt one-shot
- "sem JSONL, sem extração" removido — contradiz regra de rastreabilidade
- Critérios: adicionado "tokens capturados via JSONL" como obrigatório
- Critério de reprovação: "tokens não capturados" adicionado
- Prompt atualizado para one-shot (Read → checklist → Bash JSONL → relatório final)

## [2026-06-29] edit | procedures — documenta 2ª execução Eval 2-C (incompleta)
- 2ª execução: 1 Read call, status detectado ausente, severidade critico ✅
- Tamanho correto confirmado: 311 chars Unicode (316 bytes UTF-8); 1ª execução tinha 231 (errado)
- Bug documentado: script JSONL com range(0,n,3) captura linhas sem message.usage → retornou zeros
- Status aguarda definição do Giovani

## [2026-06-29] edit | procedures — corrige script JSONL do Eval 2-C (range fixo → filtro por usage)
- Bug: range(0,n,3) capturava linhas sem message.usage — retornava zeros
- Fix: filtrar linhas com message.usage, deduplicar por chave consecutiva (mesmo padrão v5 do 2-B)
- Prompt atualizado em "Prompt exato para a próxima sessão"

## [2026-06-29] edit | procedures — revisão geral dos evals 3-12 para nova arquitetura
- Evals 3 e 4: reescritos para nova arquitetura (sem subagentes)
- Evals 6, 7, 8, 11: linguagem atualizada (removido "agente/spawn/_meta")
- Eval 9: marcado como REMOVIDO (coordenador era componente separado, não existe na nova arch)
- Eval 10: renomeado, checklist consolidado
- Todas as ocorrências de "Critério de aprovação/reprovação" substituídas por "Critérios a serem observados"
- Todas as anotações "(era Eval X)" removidas

## [2026-06-29] edit | procedures — remove Eval 9 (coordenador) e renumera 10-12 para 9-11
- Eval 9 (coordenador isolado) removido completamente — não existe na nova arquitetura
- Evals 10, 11, 12 renumerados para 9, 10, 11
- Lista final: Evals 1, 2-A, 2-B, 2-C, 3, 4, 5, 6, 7, 8, 9, 10, 11

## [2026-06-29] edit | procedures — documenta 3ª execução Eval 2-C antes de rodar
- Entrada pré-execução adicionada: o que mudou, procedimento, resultado a preencher
- Documenta fix do script JSONL como motivação da nova tentativa

## [2026-06-29] edit | procedures — resultado 3ª execução Eval 2-C (incompleta)
- Passo 1: status identificado como ausente, critico, ~311 chars ✅
- Passo 2: SyntaxError no script — chave `cache_creation_input_tokens` truncada no prompt salvo
- Tokens não capturados — execução incompleta
- Fix necessário no prompt antes da 4ª execução

## [2026-06-29] edit | procedures — prepara 4ª execução Eval 2-C
- Causa raiz identificada: usuário copiou versão corrompida do prompt (não do arquivo)
- Aviso adicionado na seção "Prompt exato" para copiar sem editar
- 4ª execução placeholder adicionada com procedimento atualizado

## [2026-06-29] edit | procedures — documenta 4ª execução Eval 2-C (incompleta)
- Procedimento errado: invocado via /remote-control em vez de terminal fechado → /clear
- Passo 1 funcionou: status ausente detectado, 311 chars confirmados
- Passo 2 rodou contra JSONL errado (sessão anterior)
- Passo 3 não executado — usuário interrompeu
- Lição registrada: /remote-control não substitui procedimento de sessão limpa

## [2026-07-01] edit | raw — atualização de copies aprovadas em libertas.md
- Arquivo raw/libertas.md atualizado pelo usuário com copies aprovadas/atualizadas
- 151 linhas adicionadas, 117 removidas (268 linhas totais modificadas)

## [2026-07-02] edit | hermes — seção MCP Servers expandida com guia de reaproveitamento entre agentes
- Princípio documentado: MCP instala-se uma vez, registra-se por agente (Hermes via config.yaml, Claude Code via claude mcp add)
- Tabela de MCPs com comando, credenciais e onde estão registrados
- Pendência anotada: ~/.config/n8n-mcp/env não existe — MCP n8n roda sem N8N_API_KEY
- n8n MCP registrado no Claude Code nesta data

## [2026-07-02] edit | todo — duas pendências novas em proximos-passos.md
- Item 16: configurar N8N_API_KEY em ~/.config/n8n-mcp/env (MCP n8n roda sem chave)
- Item 17 [ESTUDO]: migrar info de uso geral da VPS de hermes.md para vps.md ou arquivo novo

## [2026-07-02] ingest | systems — página n8n.md criada e linkada em toda a wiki
- Nova página wiki/systems/n8n.md: Swarm queue mode (editor/webhook/worker via EasyPanel), 24 workflows (5 ativos), API, MCP, credenciais e erros conhecidos
- Pendência do N8N_API_KEY resolvida: ~/.config/n8n-mcp/env criado (chmod 600) a partir de /root/.hermes/.env; fallback $CWD/.env documentado (explica por que funcionava no Hermes e não no Claude Code)
- Wikilinks para n8n.md adicionados em: hermes.md (stack, tabela MCP, conexões), vps.md (tabela de serviços), concepts/wiki.md, concepts/orquestrador.md, todo/proximos-passos.md (item 16 marcado resolvido)
- index.md atualizado (árvore + seção systems)

## [2026-07-05] edit | tools — MCP do n8n movida para página própria n8n-mcp.md
- Regra definida pelo Giovani: plataforma é system, MCP dela é tool — todo MCP ganha página própria em tools/
- Criada wiki/tools/n8n-mcp.md: trechos de MCP de systems/n8n.md movidos verbatim (bullet MCP da Interface e erro N8N_API_KEY) + capabilities observadas no Claude Code
- systems/n8n.md: trechos movidos substituídos por wikilinks para a nova página; Conexões atualizada
- index.md atualizado (árvore + seção tools)

## [2026-07-05] ingest | tools — páginas dos MCPs do Trello (comunidade e oficial)
- wiki/tools/trello-mcp.md: MCP da comunidade (@delorenj/mcp-server-trello) — wrapper /root/mcp/trello-mcp.sh + credenciais em /root/mcp/trello.env (API key da dona do workspace validada; token pendente de geração), skill clonada em /root/.hermes/skills/trello/; status de validação pendente
- wiki/tools/trello-mcp-oficial.md: MCP oficial Atlassian (mcp.trello.com/v1, OAuth) — registrado no Claude Code mas bloqueado: "No Trello workspaces found" porque Giovani é só convidado no board (OAuth exige membro do workspace); registro mantido para teste futuro
- index.md atualizado (árvore + seção tools)

## [2026-07-06] edit | vps — túnel de porta SSH pro celular documentado + sinalização
- systems/vps.md § Acesso SSH: nova subseção "Túnel de porta" — comando validado hoje no 4G (`ssh -p 2222 -L 3777:localhost:3777 root@IPv6`), receita genérica por porta (3777 = finflow dev), passo do New session no Termux, alternativas (cloudflared, Tailscale) e aviso de nunca expor dev server; tags ganham termux/celular/ssh pra busca
- systems/termux-ssh-claude.md: nota de redirecionamento no topo apontando pra vps.md § Acesso SSH (conteúdo do diagnóstico intocado)
- Motivo: acesso pelo celular é informação recorrente; página canônica = vps.md, apontadores onde um agente procuraria

## [2026-07-06] ingest | projects — pasta nova projects/ com primeira página (finflow)
- Criada wiki/projects/ — categoria pros repositórios criados pelo Giovani (dashboards, sites, jogos); critério decidido com ele: produto próprio → projects/, serviço de infra → systems/, ferramenta de terceiro → tools/
- wiki/projects/finflow.md: página-ponteiro do finflow (o que é, /root/finflow, repo omgiova/finflow, dev na 3777, stack) apontando pra documentação completa no próprio repo (README/CLAUDE/CHANGELOG) — a wiki não duplica
- index.md atualizado (árvore + seção projects/ com o critério de categorização)

## [2026-07-06] edit | mcps — registro central de MCPs criado; Trello MCP validado e registrado
- Criada wiki/concepts/mcps.md — registro oficial dos MCPs da VPS (link por página + agentes onde cada um está registrado)
- wiki/systems/hermes.md: tabela de MCPs removida da seção MCP Servers; agora aponta pro registro central (aprovado pelo Giovani)
- wiki/tools/trello-mcp.md: credenciais validadas via curl (token da dona do workspace, conta lemosluciana); status atualizado; registro no Claude Code e no config.yaml do Hermes documentado (restart pendente de autorização)
- index.md: mcps.md adicionado (árvore + entrada); descrição do trello-mcp.md atualizada

## [2026-07-06] chore | trello-mcp — registro efetivado nos clientes
- Claude Code: MCP oficial renomeado `trello` → `trello-oficial`; comunidade registrado como `trello` (/root/mcp/trello-mcp.sh) — status ✔ Connected
- Hermes: bloco `trello` adicionado em mcp_servers: no config.yaml — vale só após restart autorizado do gateway (não executado)
- wiki/tools/trello-mcp-oficial.md: renomeação documentada

## [2026-07-06] edit | mcps — correção: MCP no Hermes NÃO precisa de restart do gateway
- Informação errada ("mudanças só valem após restart") tinha sido inserida sem verificação nos commits anteriores de hoje
- Fonte da correção: documentação oficial do Hermes (website/docs/user-guide/features/mcp.md) — auto-reload das conexões MCP ao editar config.yaml + comando /reload-mcp; confirmado pelo Giovani (MCP trello apareceu habilitado no dashboard sem restart)
- Corrigidos: wiki/concepts/mcps.md, wiki/systems/hermes.md, wiki/tools/trello-mcp.md, index.md

## [2026-07-06] edit | hermes — tabela dos 8 backends de web search suportados
- Web Search do hermes.md dizia só "Backend: Firecrawl"; adicionada tabela com os 8 backends suportados (Firecrawl, SearXNG, Brave, DDGS, Tavily, Exa, Parallel, xAI), env vars, capacidades e free tiers
- Fonte: documentação oficial do Hermes (website/docs/user-guide/features/web-search.md)
- Registrado que search_backend/extract_backend são independentes e a ordem de auto-detecção por credencial
- Páginas tocadas: wiki/systems/hermes.md

## [2026-07-06] edit | firecrawl — seção "Escopo: quem usa e por quê"
- Página lida por outro agente parecia regra geral ("preferir Firecrawl"); nova seção esclarece: no Hermes é o backend configurado (de 8 suportados), no Claude Code o padrão é a busca nativa (WebSearch/WebFetch), Firecrawl consome créditos (500/mês free)
- Registrado que os skills firecrawl-* do Claude Code instruem "usar em vez do WebFetch" por conta própria (instrução do fornecedor, não do setup)
- Conteúdo existente da página intocado; páginas tocadas: wiki/tools/firecrawl.md

## [2026-07-06] edit | trello — MCP oficial autenticado; status atualizados
- Giovani virou membro do workspace e o OAuth do MCP oficial foi autorizado na sessão do Claude Code; validado com trelloReadMember get_me (conta giovanigomesdeamorim)
- trello-mcp-oficial.md: status "Bloqueado" → "Autenticado e validado", fluxo OAuth usado documentado (tools authenticate/complete_authentication + callback localhost que fica carregando sem impedir a conclusão), 15 tools verificadas na prática (Inbox, Planner, busca, ARIs), erro "No Trello workspaces found" marcado como resolvido
- trello-mcp.md: bloqueio referido no passado; identidades distintas (comunidade = dona do workspace, oficial = Giovani); status de validação atualizado com o teste real de list_workspaces/list_boards (achou o board DEMANDAS GERAIS) e gotcha do output gigante de list_boards
- mcps.md: linha do oficial na tabela — "bloqueado" → "autenticado 2026-07-06 como Giovani"
- Referências completas fora da wiki: /root/mcp/trello-mcp-oficial-acoes.md e /root/mcp/trello-mcp-comunidade-acoes.md
- Páginas tocadas: wiki/tools/trello-mcp-oficial.md, wiki/tools/trello-mcp.md, wiki/concepts/mcps.md

## [2026-07-06] ingest | projects — projeto Automação Trello Open Mídia + página Evolution API
- Nova página projects/automacao-trello-open-midia.md: fluxo n8n "Teste-Trello-Membro-Adicionado" (SiVxXjp2euu74SRO) — Trello Trigger → Filter addMemberToCard → Switch por membro → 4 nós Evolution; validado com 2 execuções reais; macetes do payload (member vs memberCreator); IDs dos 4 membros do board; pendências e ideias futuras (backup markdown/git)
- Nova página systems/evolution-api.md (draft): gateway WhatsApp na VPS, instância Giobot, nó comunitário n8n-nodes-evolution-api, formato remoteJid; deploy/URL ainda não verificados
- systems/n8n.md: seção nova "Criar/editar workflows via API REST" com as pegadinhas verificadas (campos aceitos no PUT, settings restrito, credenciais só pela UI, active read-only)
- index.md: árvore e seções systems/ e projects/ atualizadas
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md, wiki/systems/evolution-api.md, wiki/systems/n8n.md, index.md

## [2026-07-07] edit | projects — Fluxo 1 do Trello ganha busca de card (lista, prazo, link) e novo formato de mensagem
- Novo nó HTTP "Buscar card no Trello" entre Filter e Switch: GET /1/cards/{id} com fields=name,due,shortUrl + list=true (webhook não traz vencimento nem lista)
- Mensagem reformatada (definida pelo Giovani): saudação + bloco 👤 Por / ➡️ lista / 🗓 Prazo / 🔗 link do card
- Switch e mensagens passam a referenciar o gatilho via $('Trello Trigger (DEMANDAS GERAIS)')
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md

## [2026-07-07] edit | projects — restauração das saudações personalizadas + "por Fulano" volta pra linha principal
- Saudações do Giovani (Gio~/Gabi/Lu, adicionado/adicionada) tinham sido sobrescritas por PUT via API; recuperadas do snapshot da execução 602
- Novo formato: saudação personalizada + "por Fulano" na primeira linha; bloco final só com ➡️ lista / 🗓 prazo / 🔗 link (sem linha 👤)
- Registrado incidente e regra: edição via API altera só o pedido, preserva o resto verbatim
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md

## [2026-07-07] ingest | projects — Fluxo 2 do Trello: lista semanal de prazos por membro
- Novo workflow n8n `Trello Prazos por Membro - Open Mídia` (PRaGCXrFKcmiusfE), criado via API REST, desativado; testado com sucesso pelo Manual Trigger (200 cards, sem duplicação)
- Estrutura: Schedule (segunda 8h) + Manual → Buscar cards do board (HTTP) e Buscar listas do board (HTTP, sem conexão de saída, referenciado por nome) → Code "Filtrar e agrupar por membro" → Switch por membro → 4 nós Evolution
- Code node: janela rolante de 7 dias calculada explicitamente em America/Sao_Paulo; limpeza do nome da lista (regex + exceção fixa pra LIBERTAS); agrupamento por membro > lista > prazo
- Mensagem validada: saudação com apelido (padrão do Fluxo 1), cabeçalho sem ano, nome da lista em negrito, card antes do prazo, prazo em negrito com ano
- Dois bugs achados e corrigidos durante o teste: (1) HTTP Request rodando 1x por item de entrada — encadear Buscar listas → Buscar cards causava 9x duplicação de cada card; corrigido rodando os dois em paralelo; (2) Switch com as saídas de Gabriele e Nathalia sem conexão (mensagem nunca chegava pra elas, inclusive potencialmente no Fluxo 1) — reconstruídas as 4 conexões explicitamente
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md

## [2026-07-08] edit | projects — Fluxo 2: seção "Como editar" + apelido corrigido
- Nova seção "Como editar o Fluxo 2 (pela UI do n8n, sem precisar de agente)" — onde mexer no Schedule, remoteJid, e os 3 blocos seguros do nó Code (nicknames, windowDays, texto entre crases)
- Registrado incidente: Giovani editou apelido "Giovani"→"Gio" direto no Code node, saiu "Giov" por engano, achou que tinha quebrado o fluxo — estrutura toda intacta, corrigida a string
- Testado com sucesso com número próprio e com um segundo número adicionado
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md

## [2026-07-08] edit | AGENTS.md — nova regra "Verificação de estado"
- Adicionada seção "Verificação de estado" logo após "Autorização": estado atual de sistema (processo, serviço, cron, config, dado de aplicação) sempre se verifica ao vivo (docker ps, systemctl status, crontab -l, ss -tlnp, API/arquivo do próprio sistema) antes de qualquer ação — nunca por dedução
- Motivo: sessão anterior registrou info desatualizada na memória sem verificar o Fluxo 2 do n8n; levantamento técnico da VPS nesta sessão também achou um cron quebrado (update-hermes-wiki.sh) não documentado
- Páginas tocadas: AGENTS.md

## [2026-07-08] edit | scripts/procedures — fusão dos scripts do Hermes + doc do sync automático
- Fundidos update-hermes-wiki.sh e update-hermes-api-wiki.sh em um único script (/root/scripts/update-hermes-wiki.sh), corrigindo caminho antigo (wiki/infraestrutura/ não existe mais) e bug de duplo "wiki/wiki/" no git add; script órfão apagado
- Script fundido testado com sucesso: regenerou wiki/systems/hermes-endpoints.md e criou wiki/systems/hermes-estado.md (novo — snapshot vivo de MCPs, skills, webhooks, toolsets, modelo, cron, plataformas)
- Cron semanal (segunda 3h) que estava quebrado desde a reestruturação da wiki agora funciona
- Criado wiki/procedures/sync-automatico-wiki.md documentando o cron de pull automático (*/5min) que já existia sem documentação — complementa o hook post-commit (push) já documentado
- Páginas tocadas: wiki/systems/hermes.md, wiki/systems/hermes-endpoints.md (auto), wiki/systems/hermes-estado.md (novo), wiki/concepts/wiki.md, wiki/procedures/sync-automatico-wiki.md (novo), index.md

## [2026-07-09] edit | automacao-trello — Fluxo 2 em produção documentado + Error Workflow genérico
- Corrigido status desatualizado do Fluxo 2 (Trello Prazos por Membro): estava documentado como "desativado/números de teste", na verdade ativo em produção desde 2026-07-08 com números reais dos 4 membros
- Pendências do Fluxo 2 marcadas como resolvidas: ativação, remoteJid reais, e novo Error Workflow apontado
- Criado workflow n8n "Alerta de Erro" (ID 3MI1k15YL5OUrEXF) — Error Trigger → IF (tipo de erro) → Set → Telegram; genérico, pensado para ser reaproveitado em outros workflows do n8n; documentado como uma linha na tabela de Workflows de wiki/systems/n8n.md (não como página própria — só 1 workflow usando por enquanto)
- Salvo export JSON completo do Fluxo 2 (primeira versão oficial validada/em produção) em raw/fluxo-2-trello-prazos-workflow-2026-07-09.md
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md, wiki/systems/n8n.md, raw/fluxo-2-trello-prazos-workflow-2026-07-09.md (novo), index.md

## [2026-07-09] edit | automacao-trello-open-midia — filtro anti-auto-notificação no Fluxo 1
- Nó `Só addMemberToCard` (Filter) ganhou 2ª condição: `action.data.idMember != action.memberCreator.id`, combinador `and` — descarta notificação quando o membro se adiciona a ele mesmo no card
- Editado via PUT `/api/v1/workflows/SiVxXjp2euu74SRO`, alterando só os `parameters` desse nó; demais 7 nós, conexões, credenciais e settings enviados de volta idênticos ao GET anterior (evita repetir incidente de sobrescrita)
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md

## [2026-07-12] edit | automacao-trello — Fluxo 3 (banco de dados de cards em Markdown) documentado antes da construção
- Nova seção "Fluxo 3" em wiki/projects/automacao-trello-open-midia.md: objetivo, decisões de formato (frontmatter OKF, parser tolerante de nome, mapa fixo de etiquetas, nome de arquivo) e fases de teste definidas pelo Giovani
- Não é backup: banco de dados de conteúdo em /root/om-database/ (fase 2); fase 1 é só output cru no n8n, sem salvar arquivo; SSH não aprovado
- Workflow casca: "Trello Open Mídia - Banco de Dados" (VPIpLm5pujpZvVDY), criada pelo Giovani na UI
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md

## [2026-07-12] edit | automacao-trello — Fluxo 3 construído e em teste (banco de dados de cards → GitHub)
- Seção "Fluxo 3" da página do projeto reescrita com o estado real validado: workflow VPIpLm5pujpZvVDY com Manual Trigger → Set (URL do card) → HTTP busca completa → Code markdown OKF → Filter → GitHub create→edit (repo privado omgiova/om-database, credencial do Giovani no n8n)
- Formato validado em testes de card único: type = lista limpa (sem prefixo "post"), arquivo <lista>-<formato>-<assunto>-<shortLink>.md, seções fixas com _[sem dados]_, parser tolerante, mapa de formatos verificado nos dados (R/RT/Reels teste→reels, C→carrossel, STORIE, nome vence etiqueta), canal por nome ou etiqueta
- Descartes: lista "Informações gerais" e 41 organizadores de semana (📅/SEMANA n)
- Plano antigo de gravação (pasta local + SSH) substituído por commit direto no GitHub pelo n8n
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md

## [2026-07-12] edit | automacao-trello — Fluxo 3 v1 VALIDADA pelo Giovani + JSON arquivado em raw/
- Seção do Fluxo 3 atualizada pro estado final validado: frontmatter definitivo (tags e description reservadas/vazias, etiquetas verbatim, criado extraído do ID do card, status por último; sem prazo/data_no_nome/arquivado), gravação real testada no repo GitHub omgiova/om-database
- Regra do Giovani registrada: v1 é IMUTÁVEL — automações derivadas (ex. board inteiro) devem DUPLICAR o flow no n8n, nunca editar o validado
- Bugs/lições documentados: `labels` faltando no fields da busca (etiquetas nunca chegavam); Save da UI do n8n sobrescreve edições via API (recarregar a página antes de salvar)
- JSON completo da v1 exportado via API e arquivado em raw/fluxo-3-om-database-workflow-2026-07-12.md (novo)
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md, raw/fluxo-3-om-database-workflow-2026-07-12.md (novo), index.md

## [2026-07-12] edit | automacao-trello — Fluxo 4 (board inteiro) construído, EM TESTE
- Nova seção "Fluxo 4" em wiki/projects/automacao-trello-open-midia.md: workflow "Trello Open Mídia - Banco de Dados auto" (Dl4vDai92a39Cvfy), duplicado do Fluxo 3 v1 pelo Giovani e completado via API — entrada por board inteiro (cards/all), ordenação do mais antigo pro mais novo pelo ID, fila de 1 em 1 com Wait 2s, retries, Filter→If (evita travar o loop em card ignorado), Error Workflow apontado, resumo no Telegram
- Status: aguardando teste do Giovani; seção será atualizada com o feedback e JSON irá pra raw/ quando validado
- Páginas tocadas: wiki/projects/automacao-trello-open-midia.md

## [2026-07-12] edit | projects — Fluxo 4 validado e rodada semanal incremental implementada
- Carga completa do board rodada e validada pelo Giovani (repo om-database com todos os cards atuais)
- 3 mudanças via API no workflow Dl4vDai92a39Cvfy: Schedule (Segunda 7h), fields=id,dateLastActivity, filtro de 8 dias no Code de ordenação
- Workflow publicado/ativado pelo Giovani; 1ª execução agendada esperada 2026-07-13 7h
- Página tocada: wiki/projects/automacao-trello-open-midia.md (seção Fluxo 4)

## [2026-07-12] edit | projects — removidas pseudo-pendências dos Fluxos 1, 2 e 4 (a pedido do Giovani)
- remoteJid/renomear (Fluxo 1), triggers de teste soltos (Fluxo 2) e "conferir 1ª execução" (Fluxo 4) não são pendências: fase de teste é intencional e triggers soltos não impactam nada
- Página tocada: wiki/projects/automacao-trello-open-midia.md

## [2026-07-12] ingest | projects — nova página remotion.md (automação de vídeos)
- Projeto único em /root/projects/remotion (ex remotion-video; remotion-reels excluído); HyperFrames CLI instalado na VPS
- Código dos projetos antigos do PC extraído para referencia-pc/ (um .md por projeto) + fontes em public/
- Decisão: whisper.cpp/legendas fica no PC (RAM/CPU da VPS insuficientes pro modelo medium)
- Páginas tocadas: wiki/projects/remotion.md (nova), index.md

## [2026-07-12] edit | tools — Remotion e HyperFrames reclassificados como ferramentas; projeto vira automacao-remotion
- Correção do Giovani: Remotion é ferramenta de terceiro, não projeto — projects/remotion.md removida
- Criadas tools/remotion.md e tools/hyperframes.md (seções obrigatórias de tools/)
- Criada projects/automacao-remotion.md — o projeto real do Giovani, em definição
- Páginas tocadas: tools/remotion.md, tools/hyperframes.md, projects/automacao-remotion.md, index.md

## [2026-07-12] edit | tools — remotion.md atualizada após reconstrução dos projetos do PC na VPS
- 25 composições do PC reconstruídas em src/ e verificadas; referencia-pc/ enxugada (só inventário)
- Páginas tocadas: wiki/tools/remotion.md
## [2026-07-12] edit | hyperframes — skills instaladas e setup de projetos documentado
- Pasta de projetos criada: /root/projects/hyperframes; 20 skills instaladas via `npx skills add heygen-com/hyperframes`
- Skills expostas globalmente via symlinks em /root/.claude/skills (28: 20 HyperFrames + 8 Remotion); .claude dos projetos removidas
- Página tocada: wiki/tools/hyperframes.md (Capabilities, Como usar, Configuração, Status de validação)
## [2026-07-12] edit | projects — projeto renomeado: Automação Remotion → Automação Vídeos
- Escopo é maior que Remotion (inclui HyperFrames); arquivo movido para wiki/projects/automacao-videos.md
- Links atualizados em index.md, tools/hyperframes.md e tools/remotion.md
## [2026-07-13] session | hyperframes — primeiro render real (projeto estilos-animacoes)
- Criada wiki/history/2026-07-13-primeiro-render-hyperframes.md (36s, 9:16, 6 estilos de motion; requisito seek-safe descoberto)
- tools/hyperframes.md atualizado: Erros conhecidos (seek-safe) e Status de validação (primeiro render ✓)
- index.md: nova entrada em history/ + árvore
## [2026-07-13] edit | hyperframes — projeto estilos-animacoes expandido para 12 estilos
- history/2026-07-13-primeiro-render-hyperframes.md atualizado: 12 estilos, 72s, legendas com nome técnico + parâmetros
- tools/hyperframes.md e index.md: status/descrição atualizados (72s, 12 estilos)

## [2026-07-13] edit | trello — Fluxo 1: nome da lista limpo
- Fluxo 1 (Teste-Trello-Membro-Adicionado, SiVxXjp2euu74SRO): linha `➡️ Lista:` dos 4 nós Evolution passou a mapear list.id → nome limpo (caixa normal), fallback pro nome cru
- Editado via PUT na API REST; só messageText dos 4 nós; resto verbatim; Switch e conexões verificados
- páginas tocadas: projects/automacao-trello-open-midia.md (seção Fluxo 1)
