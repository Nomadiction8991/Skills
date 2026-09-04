# Processo de Code Review — fluxo base

Modos definidos em `regras.md`. Este arquivo descreve o fluxo comum; `git.md` e `branch.md` descrevem os agents específicos de cada modo.

## 0. Garantir Context7 e reaproveitar code-review genérico

- **Context7:** identifique tecnologias no diff e use MCP `context7` (`context7.md`) se disponível. Se não estiver, instale em escopo user.
- **Reaproveitamento (sempre que o Claude Code estiver instalado, existe a skill code-review oficial):** se `~/.claude/plugins/marketplaces/claude-plugins-official/plugins/code-review/commands/code-review.md` existir, considere-a a **base genérica**. Lance um agent `general-purpose` que siga os 5 agents paralelos dela (2× CLAUDE.md compliance, 1× bugs rasos, 1× histórico/blame, 1× comentários) e o scoring 0–100 com corte 80, e devolva os achados ≥80. A skill `revisao` então **trata o retorno**: filtra falsos positivos pré-existentes, reescreve em linguagem simples, ordena do mais crítico ao menor e **controla** a saída via `formato-saida.md` — nunca posta direto pelo code-review. Se a skill oficial não estiver instalada, pule este passo e use só `validacoes.md` + checklist.

## 1. Fixar o ponto de referência

- **git/local:** `git status`; `git diff` + `git diff --cached`; `git log --oneline -5` (contexto). Falha se ambos vazios.
- **branch:** revisar a branch atual (`git branch --show-current`) contra a base/pai. Usar, nesta ordem, o 2º token, `git config --get "branch.<atual>.base"`, o upstream (`git rev-parse --abbrev-ref --symbolic-full-name @{u}`) somente se ele apontar para uma branch diferente da atual, ou o registro `branch: Created from <base>` no reflog (`git reflog show --format=%gs <atual>`). Validar com `git rev-parse <base>` e então executar `git diff <base>...HEAD` e `git log <base>..HEAD --oneline`. Se não houver uma base confiável, parar com a orientação `/revisao branch <base>`; não presumir `main`, `master` ou `develop`.
- **SHA/tag direto:** `git diff <ponto>...HEAD` + `git log <ponto>..HEAD`; validar `git rev-parse <ponto>`.

Em todos os modos, falhar se diff vazio antes de lançar agents. Agents são lançados por `git.md` ou `branch.md`.

## 2. Ler arquivos completos sem ampliar o escopo

Não revise só o diff — abra os arquivos inteiros que o diff toca para entender o contexto. O diff esconde o método irmão que já valida, o outro lugar que calcula o mesmo dado e o padrão dos vizinhos. Esses arquivos e buscas adicionais são apenas contexto: nenhum problema fora do diff pode virar achado.

## 3. Passar o checklist obrigatório (genérico do code-review + 7 eixos + 15 validações pontuais)

Analise todos os eixos internamente, mas mostre no relatório somente os achados:

1. Autorização e escopo do dono do dado
2. Mesma informação derivada de formas divergentes
3. Código morto introduzido
4. Estado assíncrono e timers no front
5. Consistência com os arquivos vizinhos
6. Correção e borda dos dados
7. Testes

Primeiro, se o passo 0 retornou achados genéricos do code-review, mantenha-os como base. Depois, passar as 15 validações pontuais de `references/validacoes.md` (autorização, divergência, código morto, indireção, duplicação, responsabilidade, API simples, separação de MR, padrões do stack, infra, pipeline/config, migração, idioma consistente, rodar testes, comentários só quando necessário) — são o diferencial que o genérico não pega. Inclua `code-smells.md` dentro dos eixos 3/5. A skill `revisao` controla e reescreve tudo: filtra <80, remove pré-existente fora do diff e reordena.

## 3.5 Rodar todos os testes (sempre)

Identifique como rodar os testes no repo em que está — procure nesta ordem: `Makefile` (`make test`/`make tests`/`make check`), `README.md`/`AGENTS.md`/`CONTRIBUTING.md`, `package.json` (`scripts.test`), `composer.json`, `pyproject.toml`/`tox`, `cargo test`, `./test.sh`, `docker exec` etc. Leia o arquivo e extraia o comando exato. Rode a suíte completa para validar o diff. Só reporte falhas causadas pelo conteúdo revisado; falhas pré-existentes ou sem relação ficam fora do relatório. Se não houver como rodar (sem Docker/env), declare "testes não executados — motivo" em vez de silenciar.

## 4. Validar antes de reportar

Para cada achado, antes de escrever:
- Abra o arquivo e confirme internamente no código atual; guarde `arquivo:linha` apenas para validação, nunca no relatório
- Construa cenário concreto de falha ("usuário B abre /pedido/17 → vê pedido do A"); se não conseguir, descarte
- Confirme que o problema foi introduzido pelo diff ou está diretamente no conteúdo revisado; se já existia antes, não foi tocado ou não tem relação direta, descarte
- Não inflar severidade
- Não altere nem edite arquivos de código. As validações são puramente analíticas.

## 5. Agregar

Una os dois eixos em um único relatório direto, seguindo obrigatoriamente `references/formato-saida.md`:

1. Cabeçalho conforme o modo: `Revisão das alterações não commitadas (N arquivos). X achados, <resumo>.` — `X` conta os achados introduzidos pelo diff
2. Lista numerada **sempre do mais crítico ao menor, todo achado dentro do escopo numerado inclusive menor**: `1. Título — severidade` + explicação simples + `Como corrigir: ...`, sem nomes ou caminhos técnicos (máx 5; se houver mais, manter os 5 mais relevantes). `Como corrigir` é apenas uma orientação conceitual em texto, **nunca** uma alteração a ser aplicada agora.
3. `Veredicto: ...` (uma linha — só sobre o diff atual como está)
4. `Resumo final: ...` conforme o modelo de `references/formato-saida.md` (sempre retratando o estado real encontrado com seus achados pendentes de decisão do usuário, **nunca** resumindo como "já corrigido")
5. Pergunta final para decisão do usuário (sem nunca aplicar alterações antes).

Não crie seções `## Padrões` / `## Especificação` separadas e não duplique `Linguagem simples / técnica` — mostre direto o achado em linguagem simples. Se corrigir relatório anterior, acrescente antes do veredicto: `Correção de relatório anterior: ...`
