# Processo — fluxo comum (Parte A) + modo ponto fixo (Parte B)

## Parte A — comum aos 3 modos

### A0. Context7 e técnica oficial

Técnica `code-review` adaptada: fonte única em `regras.md` (steps 1-8). Aqui só orquestração: gere o resumo do diff (1-2 frases), colete `AGENTS.md` por diretório + `REVIEW.md` se existir. Se diff vazio, falha; se trivial (só `*.lock`, `src/gen/**`, vendored), retorne `Nenhum achado — diff trivial` sem agents. Context7: ver `context7.md` (opcional, nunca instalar sozinho).

### A1. Fixar o ponto de referência

| Modo | Comando |
|---|---|
| git/local | `git status`; `git diff` + `git diff --cached`; `git log --oneline -5` |
| branch | `git diff <base>...HEAD` + `git log <base>..HEAD` (base conforme `branch.md`) |
| ponto fixo | `git diff <ponto>...HEAD` + `git log <ponto>..HEAD`; validar `git rev-parse <ponto>` |

Detalhe de cada modo no seu arquivo (`git.md`, `branch.md`, Parte B abaixo).

Continuidade em worktree (só leitura até aqui): se o usuário pedir depois uma ação Git em um worktree, encerre o escopo da revisão e confirme o destino (`pwd`, `git rev-parse --show-toplevel`, `git status --short` no alvo) antes de orientar ou executar. Nunca reutilize caminho/branch/status de outro worktree; sem destino claro, pergunte.

### A2. Ler arquivos + comparar antes/depois

Abra por inteiro os arquivos que o diff toca (o diff esconde o método irmão que já valida). Abra também o **antes** (base) dos trechos-chave: mensagem de erro antiga vs nova (a causa sumiu?); função removida vs nova (o mapeamento veio junto?); ordenação/permissão antiga vs nova. Quem só lê o depois não vê regressão. Tudo fora do diff é só contexto: nenhum problema externo vira achado (ver `regras.md`, Escopo).

### A3. Checklist (genérico + 7 eixos + 24 validações)

Primeiro os achados genéricos do code-review (se houve). Depois as 24 validações de `validacoes.md` — cada agent cobre só seu subconjunto (`agents.md`, Donos). Para cada validação 16–24, o orquestrador exige evidência explícita no relatório interno (trecho antes/depois ou "verificado sem achado"); pular em silêncio reprova a revisão. `code-smells.md` apoia B2 dentro de #4/#5.

### A3.5. Rodar todos os testes (orquestrador, sempre)

Descubra o comando nesta ordem: `Makefile` (`make test`/`tests`/`check`), `README.md`/`AGENTS.md`/`CONTRIBUTING.md`, `package.json` (`scripts.test`), `composer.json`, `pyproject.toml`/`tox`, `cargo test`, `./test.sh`, `docker exec`. Rode a suíte completa assim que diff e comando existirem. Só reporte falha causada pelo conteúdo revisado; pré-existente fica fora. Sem como rodar, declare "testes não executados — motivo".

### A4. Validação e agregação

Validação 1:1 e filtros em `agents.md` (pós-agents); ranking sem teto em `regras.md` step 8. Agregue abaixo:

1. Cabeçalho do modo (`X` = achados deduplicados do diff)
2. Lista numerada do mais crítico ao menor, todos os validados sem teto: `1. Título — severidade` + explicação simples + `Como corrigir: ...` + `Onde: ...`. `Como corrigir` é orientação conceitual, **nunca** alteração a aplicar agora.
3. `Veredicto: ...` (uma linha, só sobre o diff como está)
4. `Resumo final: ...` (escopo, X achados, conclusão — sempre fiel aos achados pendentes, **nunca** "já corrigido")
5. Pergunta final (sem aplicar nada antes)

Sem seções `## Padrões`/`## Especificação` separadas e sem blocos `Linguagem simples / técnica` — achado direto em linguagem simples. Se corrigir relatório anterior, acrescente antes do veredicto: `Correção de relatório anterior: ...`. Se dois achados colidirem no mesmo trecho, mantenha o de maior confiança.

## Parte B — particular do modo ponto fixo (SHA/tag)

`args` é commit ou tag (`HEAD~N`, SHA, `v1.2.0`): valide com `git rev-parse <ponto>`, capture `git diff <ponto>...HEAD` + `git log <ponto>..HEAD --oneline` e aplique a Parte A na ordem (A0→A3.5), lançando os 4 agents de `agents.md` com esse diff. Cabeçalho: `Revisão desde <ponto> (N arquivos, M commits). X achados, <resumo>.`
