# Processo de Code Review — fluxo base

Modos definidos em `regras.md`. Este arquivo descreve o fluxo comum; `git.md` e `branch.md` descrevem os agents específicos de cada modo.

## 0. Garantir Context7 e reaproveitar técnica code-review oficial (adaptada, sem modo PR)

Fonte: `anthropics/claude-code/plugins/code-review` — README + `code.claude.com/docs/en/code-review`. Extração já consolidada em `regras.md` (técnica oficial adaptada, steps 1-8). Aqui só o essencial para orquestração:

- **Context7 (opcional):** identifique tecnologias no diff e use MCP `context7` (`context7.md`) se disponível. Se não estiver, siga sem ele — não instale automaticamente. Preserve configurações existentes.
- **CLAUDE.md → AGENTS.md + REVIEW.md:** mapeie `CLAUDE.md` do oficial para `AGENTS.md` por diretório. Se houver `REVIEW.md` na raiz, use como instrução só-de-revisão (severidade, caps, skip rules, checks específicos) — ela complementa `AGENTS.md` e é lida como texto puro (sem `@import`).
- **Resumo do diff (novo, extraído do oficial step 3):** antes de lançar agents, gere 1-2 frases: quantos arquivos, intenção aparente do diff. Envie o resumo a todos os agents para reduzir alucinação.
- **Checar se revisão é necessária (oficial step 1):** se `git diff` vazio ou só `*.lock`/`*.gen/**`/gerado, declare `Nenhum achado — diff trivial` e encerre sem lançar agents.
- **4 agents paralelos (ver `regras.md` step 4 e detalhes em `git.md`/`branch.md`):** A1+A2 compliance, B1 bugs diff-only, B2 histórico/blame. Scoring 0-100 corte 80, validação paralela 1:1, filtros, deduplicação e ranking — tudo controlado pela `revisao` via `formato-saida.md`. Se oficial não estiver instalada, pule para `validacoes.md` + 7 eixos.

Compatibilidade: o genérico pega bugs rasos + AGENTS compliance; `validacoes.md` (15) + 7 eixos pegam o que reprova na prática (autorização, divergência, código morto etc.). `processo.md#3` une ambos; `processo.md#3.5` continua rodando testes sempre. Limiar 80 configurável via `regras.md`.

## 1. Fixar o ponto de referência

- **git/local:** `git status`; `git diff` + `git diff --cached`; `git log --oneline -5` (contexto). Falha se ambos vazios.
- **branch:** revisar a branch atual (`git branch --show-current`) contra a base/pai. Usar, nesta ordem, o 2º token, `git config --get "branch.<atual>.base"`, o upstream (`git rev-parse --abbrev-ref --symbolic-full-name @{u}`) somente se ele apontar para uma branch diferente da atual, ou o registro `branch: Created from <base>` no reflog (`git reflog show --format=%gs <atual>`). Validar com `git rev-parse <base>` e então executar `git diff <base>...HEAD` e `git log <base>..HEAD --oneline`. Se não houver uma base confiável, parar com a orientação `/revisao branch <base>`; não presumir `main`, `master` ou `develop`.
- **SHA/tag direto:** `git diff <ponto>...HEAD` + `git log <ponto>..HEAD`; validar `git rev-parse <ponto>`.

Em todos os modos: falhar se diff vazio antes de lançar agents; se diff trivial (só `*.lock`, `src/gen/**`, vendored), retorne `Nenhum achado — diff trivial` sem agents. Agents são lançados por `git.md` ou `branch.md`.

## 2. Ler arquivos completos sem ampliar o escopo

Não revise só o diff — abra os arquivos inteiros que o diff toca para entender o contexto. O diff esconde o método irmão que já valida, o outro lugar que calcula o mesmo dado e o padrão dos vizinhos. Esses arquivos e buscas adicionais são apenas contexto: nenhum problema fora do diff pode virar achado.

Também gere o resumo do diff (1-2 frases) e colete `AGENTS.md` por diretório + `REVIEW.md` se existir na raiz (ver `regras.md` step 2). Envie resumo + lista de guias para todos os agents.

## 3. Passar o checklist obrigatório (genérico do code-review + 7 eixos + 15 validações pontuais)

Analise todos os eixos internamente, mas mostre no relatório somente os achados:

1. Autorização e escopo do dono do dado
2. Mesma informação derivada de formas divergentes
3. Código morto introduzido
4. Estado assíncrono e timers no front
5. Consistência com os arquivos vizinhos
6. Correção e borda dos dados
7. Testes

Primeiro, se o passo 0 retornou achados genéricos do code-review, mantenha-os como base. Depois, passar as 15 validações pontuais de `references/validacoes.md` (autorização, divergência, código morto, indireção, duplicação, responsabilidade, API simples, separação de MR, padrões do stack, infra, pipeline/config, migração, idioma consistente, rodar testes, comentários só quando necessário) — são o diferencial que o genérico não pega. Inclua `code-smells.md` dentro dos eixos 3/5. A skill `revisao` controla e reescreve tudo: filtra <80, deduplica, remove pré-existente fora do diff e reordena.

> Correção: `validacoes.md` contém 15 validações (o cabeçalho antigo dizia 14 por engano).

## 3.5 Rodar todos os testes (sempre)

Identifique como rodar os testes no repo em que está — procure nesta ordem: `Makefile` (`make test`/`make tests`/`make check`), `README.md`/`AGENTS.md`/`CONTRIBUTING.md`, `package.json` (`scripts.test`), `composer.json`, `pyproject.toml`/`tox`, `cargo test`, `./test.sh`, `docker exec` etc. Leia o arquivo e extraia o comando exato. Rode a suíte completa para validar o diff. Só reporte falhas causadas pelo conteúdo revisado; falhas pré-existentes ou sem relação ficam fora do relatório. Se não houver como rodar (sem Docker/env), declare "testes não executados — motivo" em vez de silenciar.

## 4. Validar antes de reportar

Para cada achado ≥80, lance 1 validador paralelo (modelo do autor: forte para B1/B2, leve para A1/A2) que:

- Reabre o arquivo e confirma `arquivo:linha` no código atual + cenário concreto ("usuário B abre /pedido/17 → vê pedido do A"); guarde `arquivo:linha` só para validação, nunca no relatório
- Confirma que o problema foi introduzido pelo diff ou está diretamente no conteúdo revisado; se já existia antes, não foi tocado ou não tem relação direta, descarte
- Verifica citação explícita de `AGENTS.md`/`REVIEW.md` para achados de compliance; sem citação literal, descarte
- Não inflar severidade; não alterar arquivos

Descarte não confirmados. Depois deduplique achados que falam do mesmo trecho/causa antes de agregar.

## 5. Agregar

Una os dois eixos em um único relatório direto, seguindo obrigatoriamente `references/formato-saida.md`:

1. Cabeçalho conforme o modo: `Revisão das alterações não commitadas (N arquivos). X achados, <resumo>.` — `X` conta os achados introduzidos pelo diff (deduplicados)
2. Lista numerada **sempre do mais crítico ao menor, todo achado dentro do escopo numerado inclusive menor**: `1. Título — severidade` + explicação simples + `Como corrigir: ...`, sem nomes ou caminhos técnicos (máx 5; se houver mais, manter os 5 mais relevantes). `Como corrigir` é apenas uma orientação conceitual em texto, **nunca** uma alteração a ser aplicada agora.
3. `Veredicto: ...` (uma linha — só sobre o diff atual como está)
4. `Resumo final: ...` conforme o modelo de `references/formato-saida.md` (sempre retratando o estado real encontrado com seus achados pendentes de decisão do usuário, **nunca** resumindo como "já corrigido")
5. Pergunta final para decisão do usuário (sem nunca aplicar alterações antes).

Não crie seções `## Padrões` / `## Especificação` separadas e não duplique `Linguagem simples / técnica` — mostre direto o achado em linguagem simples. Se corrigir relatório anterior, acrescente antes do veredicto: `Correção de relatório anterior: ...`

Se dois achados colidirem no mesmo trecho, mantenha o de maior confiança e descarte o outro na deduplicação.
