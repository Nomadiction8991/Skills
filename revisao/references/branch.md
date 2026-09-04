# Modo Branch — /revisao branch [base]

Revisa a branch atual (`HEAD`) inteira contra a base/pai explícita ou detectada conforme `regras.md`. Sempre chama agents com contexto limpo. Nunca posta sem permissão explícita de uso único (`regras.md`). Captura do diff conforme `processo.md#1` (verifica diff trivial e resume diff antes de lançar agents).

## 1. Ler arquivos completos + diff da branch + resumir

Abrir arquivos inteiros tocados + diff completo da branch (`git diff <base>...HEAD`). O diff isolado esconde validação do irmão e padrão dos vizinhos. Gere resumo 1-2 frases (quantos arquivos/commits, intenção) para alimentar os agents.

## 2. Lançar agents (contexto limpo) — 4 paralelos adaptados (extraídos do oficial)

Disparar em paralelo com `general-purpose` (só leitura; proibido editar, corrigir, bloco de sugestão ou link SHA). Cada agent recebe: diff `git diff <base>...HEAD` + resumo + lista `git log <base>..HEAD`, arquivos completos tocados e `AGENTS.md` por diretório + `REVIEW.md` se existir + `validacoes.md` colada.

**Passo prévio:** colete `AGENTS.md` da raiz + pastas dos arquivos do branch diff + `REVIEW.md` da raiz se existir. Instrua escopo por diretório como em `git.md` (só avalie guia cujo caminho é prefixo do arquivo; se não houver guia, pule compliance).

**Agent A1 — AGENTS compliance (branch, parte 1):**
Briefing: "Audite conformidade AGENTS.md/REVIEW.md para branch atual vs base. Verifique cada arquivo tocado por `git diff <base>...HEAD` contra guias cujo caminho é prefixo do arquivo. Cite regra literal violada. Só HIGH SIGNAL inequívoco. Compare com padrão da branch base: se base já violava e diff não introduziu, descarte (pré-existente). Use validacoes#8 (separação refatoração vs feature — se mesmo diff mistura renomeação e nova regra, aponte), #9 e #13 como apoio mas só reporte com citação de guia. Respeite REVIEW.md skip rules e cap de nits. Dê nota 0-100 (0 falso, 25 talvez, 50 moderado, 75 altamente confiante, 100 certeza — mapeado 0-25 pré-existente, 50-75 baixo impacto, 76-90 importante, 91-100 crítico), mantenha ≥80, informe arquivo:linha+cenário para validação. Apenas aponte, nunca altere. Até 340 palavras."

**Agent A2 — AGENTS compliance (branch, parte 2, redundância):**
Briefing: "Segundo auditor AGENTS.md/REVIEW.md em paralelo, independente de A1, mesmo diff de branch e mesmos guias por diretório. Re-audite com foco complementar: convenções de API, estrutura de pastas, tratamento de erro, logs e testes citados nos guias. Só HIGH SIGNAL com citação. Respeite REVIEW.md caps. Dê nota 0-100 mesmo critério, filtre ≥80, descarte pré-existente, linter e silenciado por lint-ignore. Informe arquivo:linha+cenário para validação paralela. Só diagnóstico textual. Até 340 palavras."

**Agent B1 — Bugs rasos diff-only (branch):**
Briefing: "Detector de bugs óbvios no diff da branch (`<base>...HEAD`), sem abrir contexto extra além do diff + resumo. Aponte só falhas certas no código introduzido pela branch: falha de compilação/parse, import/tipo faltando, lógica que sempre dá resultado errado (ex: mesmo dado calculado diferente em listagem vs detalhe — validacoes#2), falta de filtro por dono/tenant que expõe dado ao trocar id (validacoes#1), borda não tratada (validacoes#6). Ignore nitpick e problemas que dependem de estado específico. Dê nota 0-100 (mapeamento 0 falso, 50 moderado, 75 importante, 100 crítico), mantenha ≥80, informe arquivo:linha do diff e cenário concreto de falha. Apenas aponte. Até 320 palavras."

**Agent B2/C — Histórico/blame + consistência/timers (branch, híbrido):**
Briefing: "Analista histórico/blame + consistência para branch. Recebe diff + resumo + arquivos completos + git blame/log dos trechos. Verifique código introduzido pela branch: segurança/lógica incorreta com contexto histórico, código morto/duplicação/indireção (validacoes#3,4,5), responsabilidade no lugar certo (#6), padrões do stack (#9), infra enxuta (#10), pipeline/config (#11), migração (#12) com procedimento faltando, idioma (#13). Compare com método irmão da base que já valida. Descarte pré-existente via blame. Se diff tocar frontend, verifique também consistência com vizinhos (nomenclatura, camadas, AGENTS.md do diretório) e estado assíncrono/timers (validacoes#4 + processo#4: timer sem limpeza, estado compartilhado) — só reporte se introduzido na branch. Dê nota 0-100, mantenha ≥80, informe arquivo:linha+cenário. Híbrido cobre histórico e, quando houver frontend, acumula papel C — mantém 4 agents fixos. Apenas diagnóstico textual. Até 350 palavras."

**Pós-agents — scoring, validação, deduplicação e filtros (branch):**
Mesmo que `git.md`: para cada achado ≥80 lance validador paralelo que confirma arquivo:linha+cenário, escopo de guia e cenário concreto. Aplique filtros traduzidos (pré-existente, parece bug mas correto, pedante, linter, qualidade geral sem regra, silenciado). Filtre <80, descarte não validados, deduplique achados do mesmo trecho/causa (mantenha maior confiança), reescreva em linguagem simples, ordene bloqueia/importante/menor, máx 5, `Como corrigir:` só textual, sem SHA. Se branch não tocar frontend, B2/C simplesmente não reporta timers/consistência — mantém 4 agents fixos sem pular."

## 3. Validar antes de agregar

Mesmo que modo branch: confirmar internamente o arquivo e a linha no código atual, além do cenário concreto e da relação direta com o diff; dedup se colidir com outro; não mostrar esses detalhes no relatório. Descartar problemas pré-existentes ou fora do escopo, sem inflar severidade.

## 4. Agregar

Seguir `formato-saida.md` + `processo.md#5`. Mostrar o relatório ao usuário, sempre incluindo o `Resumo final` antes da pergunta final. Cabeçalho: `Revisão da branch <atual> vs <base> (N arquivos, M commits). X achados, <resumo>.` (`X` deduplicado, ignora triviais). Nunca alterar arquivos, commitar, corrigir ou postar durante a revisão.
