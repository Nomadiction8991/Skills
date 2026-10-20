# Modo Branch — /revisao branch [base]

Revisa a branch atual (`HEAD`) contra a base/pai explícita ou detectada. Briefings e pós-agents em `references/agents.md` — aqui só a captura do modo. Nunca posta sem permissão explícita de uso único (`regras.md`).

## 1. Resolver a base e capturar o diff

A base é, nesta ordem: 2º token; `git config --get "branch.<atual>.base"`; upstream (`git rev-parse --abbrev-ref --symbolic-full-name @{u}`) somente se apontar para branch diferente da atual; ou o registro `branch: Created from <base>` no reflog. Não assumir `main`, `master` ou outra base por convenção; se indeterminável com segurança, interrompa e peça `/revisao branch <base>`.

Valide com `git rev-parse <base>` e execute `git diff <base>...HEAD` + `git log <base>..HEAD --oneline`. Se diff vazio, falha; se trivial, retorne `Nenhum achado — diff trivial` sem agents (padrões de trivial em `processo.md`, A0).

## 2. Ler arquivos + resumir

Abrir por inteiro cada arquivo tocado + resumo em 1-2 frases (N arquivos, M commits, intenção). Colete `AGENTS.md` da raiz + pastas dos arquivos + `REVIEW.md` se existir (só vale guia cujo caminho é prefixo do arquivo).

## 3. Lançar agents e agregar

Lance os 4 agents de `agents.md` com essa entrada (somente leitura) e agregue via pós-agents + `processo.md` (Parte A, A4).

Cabeçalho: `Revisão da branch <atual> vs <base> (N arquivos, M commits). X achados, <resumo>.` Sempre incluir o `Resumo final` antes da pergunta final. Nunca alterar arquivos, commitar, corrigir ou postar durante a revisão.
