# Modo Git / Local — /revisao e /revisao git

Revisa alterações não commitadas. Briefings e pós-agents em `references/agents.md` — aqui só a captura do modo.

## 1. Capturar o diff

`git status`; `git diff` + `git diff --cached`; `git log --oneline -5` (contexto). Falha se ambos os diffs vazios. Se diff trivial, retorne `Nenhum achado — diff trivial` sem agents (padrões de trivial em `processo.md`, A0).

## 2. Ler arquivos + resumir

Abrir por inteiro cada arquivo tocado pelo diff (o diff esconde o método irmão que já valida). Gere o resumo em 1-2 frases (arquivos + intenção) e colete `AGENTS.md` da raiz + pastas dos arquivos + `REVIEW.md` se existir.

## 3. Lançar agents e agregar

Lance os 4 agents de `agents.md` com essa entrada (somente leitura) e agregue via pós-agents + `processo.md` (Parte A, A4).

Cabeçalho: `Revisão das alterações não commitadas (N arquivos). X achados, <resumo>.` Sempre incluir o `Resumo final` antes da pergunta final. Nunca alterar arquivos, commitar ou aplicar correções durante a revisão.
