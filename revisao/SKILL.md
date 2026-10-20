---
name: revisao
model: opus
effort: medium
description: "Revisa código como revisor sênior antes do merge em modo somente leitura. This skill should be used when the user says 'revisa isso', 'olha o que vai pro MR', 'está pronto pra mergear?', 'revisa a branch', 'revisa esse diff/commit', 'pode revisar', 'review' — mesmo sem /revisao. Be sure to use this skill whenever a diff, branch or commit needs senior review before merge, even if the user doesn't type /revisao or says it in natural language. Do NOT use for committing changes or creating MRs — read-only review."
argument-hint: "[git | branch [base] | <sha|tag>]"
allowed-tools: Read Bash Grep Glob mcp__gitlab__*
---

# Revisão de Branch

Revisão diagnóstica, somente leitura, em 3 modos. Cada modo captura seu diff e lança os mesmos 4 agents (`agents.md`); a skill filtra, reescreve em linguagem simples e formata via `formato-saida.md`. Escopo, técnica e ranking: `regras.md` (fonte da verdade — leia primeiro).

## Roteamento — leia só o que o argumento pede

- `args` vazio ou `git` → `git.md` (diff local) → `agents.md`
- `args` começa com `branch` → `branch.md` (branch atual vs base) → `agents.md`
- `args` é SHA/tag (`HEAD~N`, `v1.2.0`) → `processo.md` Parte B (ponto fixo) → `agents.md`

Fluxo comum a todos: `processo.md` Parte A. Não leia referências que o modo não pede. Permissão de uso único em `regras.md`.

## Arquivos de referência

- `references/regras.md` — **fonte da verdade**: modos, técnica code-review, escopo, ranking sem teto, regras rígidas de leitura/permissão
- `references/git.md` — modo git/local: só a captura do diff
- `references/branch.md` — modo branch: só resolução da base + captura
- `references/agents.md` — os 4 agents compartilhados (briefings + pós-agents; tabela de donos em `validacoes.md`)
- `references/processo.md` — Parte A fluxo comum + Parte B modo ponto fixo
- `references/validacoes.md` — 24 validações genéricas + tabela de donos (cada agent lê só seu subconjunto)
- `references/revisao-exigente.md` — gates de tamanho/mistura, dor primeiro, checklist pré-MR
- `references/formato-saida.md` — formato obrigatório do relatório (explicação simples + `Como corrigir` + `Onde:`)
- `references/code-smells.md` — baseline complementar do Detetive (dentro de #4 e #5)
- `references/context7.md` — quando e como usar o MCP context7 (opcional)
- evals/evals.json — evals (prompt → saída esperada + assertions); `evals/run.md` — como rodar

## Exemplo
Usuário: "revisa a branch" → resolve a base (`branch.md`), roda `git diff <base>...HEAD`, resume em 1-2 frases, lança `agents.md` e agrega via `processo.md` (A4) + `formato-saida.md`.
## Troubleshooting
- Base indeterminável com segurança → interrompe e pede `/revisao branch <base>` explícita.
- Achado fora do diff ou pré-existente → descarta, não entra no relatório.
