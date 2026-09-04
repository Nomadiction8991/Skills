# Modo Git / Local — /revisao e /revisao git

Revisa alterações não commitadas. Sempre chama agents com contexto limpo. Captura do diff conforme `processo.md#1` (verifica diff trivial e resume diff antes de lançar agents).

## 1. Ler arquivos completos + resumir diff

Abrir cada arquivo tocado pelo diff por inteiro. O diff esconde método irmão que já valida e o lugar que já calcula o mesmo dado. Gere resumo 1-2 frases (arquivos tocados + intenção) para alimentar os agents.

## 2. Lançar agents (contexto limpo) — 4 paralelos adaptados (extraídos do oficial)

Disparar em paralelo com `general-purpose` (estritamente leitura/análise; proibido `Write`/`Edit`/`Bash` de escrita, proibido aplicar correções, proibido bloco de sugestão, proibido link com SHA). Cada agent recebe: diff (`git diff` + `git diff --cached`), resumo do diff, lista de commits (`git log --oneline -5`), arquivos completos tocados e lista de `AGENTS.md` por diretório + `REVIEW.md` se existir + `validacoes.md` colada no prompt.

**Passo prévio (orquestrador, antes dos 4):** colete `AGENTS.md` da raiz e de cada pasta pai dos arquivos do diff + `REVIEW.md` da raiz se existir. Envie a lista para todos os agents. Instrua: só avalie regra de `AGENTS.md`/`REVIEW.md` cujo caminho é prefixo do arquivo analisado; se não houver guia, pule compliance e mantenha só bugs/histórico. Instrua também: ignore diff trivial (`*.lock`, `src/gen/**`, vendored).

**Agent A1 — AGENTS compliance (parte 1):**
Briefing: "Você é auditor de conformidade AGENTS.md/REVIEW.md. Recebe diff local (não commitado), resumo, arquivos completos e lista de AGENTS.md por diretório + REVIEW.md. Verifique só arquivos tocados pelo diff contra regras explícitas dos guias que compartilham caminho com o arquivo. Cite a regra exata violada. Só aponte HIGH SIGNAL: violação inequívoca + quebra ou comportamento errado garantido. Ignore estilo, nitpick, linter e qualidade geral sem regra explícita. Para cada achado dê nota 0-100 (0 falso positivo, 25 talvez real, 50 moderado menor, 75 altamente confiante, 100 certeza — mapeado em 0-25 pré-existente, 26-50 menor não citado, 51-75 baixo impacto, 76-90 importante, 91-100 crítico com citação literal), mantenha só ≥80 internamente, informe arquivo:linha e cenário concreto só para validação. Use validacoes#13 (idioma) e #9 (padrões do stack) como apoio, mas só reporte se AGENTS.md/REVIEW.md citar. Apenas aponte problemas, nunca altere arquivos. Sugestão só textual curta. Até 330 palavras, linguagem simples no achado. Retorne lista com título, nota, arquivo:linha, regra citada e cenário."

**Agent A2 — AGENTS compliance (parte 2, redundância):**
Briefing: "Você é segundo auditor AGENTS.md/REVIEW.md em paralelo (redundância com A1). Mesmo input e mesmas regras de escopo por diretório. Re-audite independentemente os mesmos arquivos tocados contra guias. Não veja saída do A1. Foque em regras que A1 pode ter perdido: nomenclatura, camadas, convenções de import, tratamento de erro, logs, testes e compatibilidade citadas em AGENTS.md/REVIEW.md. Respeite `REVIEW.md` caps de nit e skip rules (ex.: gerados/lock). Só HIGH SIGNAL com citação literal. Dê nota 0-100 com mesmo critério, filtre ≥80, descarte pré-existente, descarte silenciado por lint-ignore, descarte o que linter pegaria. Para cada achado informe arquivo:linha e cenário concreto para validação paralela. Apenas diagnóstico textual, nunca altere arquivos, nunca bloco de sugestão, nunca link SHA. Até 330 palavras."

**Agent B1 — Bugs rasos diff-only:**
Briefing: "Você é detector de bugs óbvios. Recebe SÓ o diff (git diff + cached) + resumo, sem abrir arquivos completos além do diff e sem git blame. Procure só no código introduzido: quebra de compilação/parse (sintaxe, tipo, import faltando, referência não resolvida), resultado errado garantido independente de input (lógica claramente invertida, cálculo divergente, validação faltando que expõe dado). Use validacoes#1 (autorização/escopo), #2 (mesma informação divergente), #6 (borda dos dados) e #3 (código morto) como lentes, mas só reporte se falha for certa. Ignore nitpick, estilo, qualidade geral e problemas que dependem de input específico. Dê nota 0-100 (0 parece bug mas correto, 25 talvez, 50 moderado, 75 altamente confiante, 100 certeza — mapeado 0-25 correto, 51-75 baixo impacto, 76-90 importante, 91-100 crítico), mantenha só ≥80. Para cada achado informe arquivo:linha do diff e cenário de falha concreto. Apenas aponte, nunca corrija. Até 320 palavras."

**Agent B2 — Histórico/blame + lógica introduzida:**
Briefing: "Você é analista de histórico e lógica introduzida. Recebe diff + resumo + arquivos completos + git blame/log dos trechos tocados. Verifique problemas no código introduzido que só aparecem com contexto histórico: segurança, lógica incorreta, dado exposto por falta de escopo, divergência entre listagem/detalhe, recurso adicionado e não usado (código morto), duplicação de constante/query já existente. Compare com método irmão que já valida e com padrão dos vizinhos; se irmão valida e este não, é bug. Use validacoes#1,2,3,4,5,6,8 + code-smells como apoio. Cheque blame: se trecho já existia antes e não foi tocado, descarte (pré-existente). Dê nota 0-100 (mesmo mapeamento), mantenha ≥80, informe arquivo:linha e cenário concreto (ex: trocar id na URL expõe dado de outro tenant). Se diff tocar frontend, inclua também checklist #4 de processo (estado assíncrono/timers, limpeza em unmount) mas só se introduzido no diff. Apenas diagnóstico textual, nunca altere arquivos. Até 340 palavras."

**Pós-agents — scoring, validação paralela, deduplicação e filtros (orquestrador):**
Após retorno dos 4, para cada achado ≥80 lance 1 sub-agent validador em paralelo (mesmo modelo do autor: validador forte para B1/B2, validador leve para A1/A2) que reabre arquivo e confirma arquivo:linha + cenário + citação de guia para compliance. Aplique filtros de falsos positivos traduzidos: pré-existente, parece bug mas correto, pedante, linter, qualidade geral sem regra AGENTS.md/REVIEW.md, silenciado. Descarte <80 e não validados. Deduplique achados do mesmo trecho/causa (mantenha maior confiança). Reescreva sobreviventes em linguagem simples, ordene 91-100→bloqueia, 80-90→importante/menor conforme impacto, limite a 5, mapeie para `formato-saida.md` com `Como corrigir:` textual apenas (sem bloco aplicável, sem SHA). Cada agent já recebeu validacoes colada — não precisa reler."

## 3. Validar antes de agregar

Para cada achado dos agents: confirmar internamente no código atual, construir cenário concreto ("uma pessoa abre um pedido e vê dados de outra"), descartar se não descrever, se estiver fora do diff ou se for pré-existente, dedup se colidir com outro, não inflar severidade. Reescrever o resultado sem nomes de código ou caminhos técnicos.

## 4. Agregar

Seguir `formato-saida.md` e `processo.md#5`. Cabeçalho: `Revisão das alterações não commitadas (N arquivos). X achados, <resumo>.` (`X` deduplicado). Sempre incluir o `Resumo final` antes da pergunta final. Nunca alterar arquivos, commitar ou aplicar correções durante a revisão.
