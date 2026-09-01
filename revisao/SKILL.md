---
name: revisao
model: sonnet
effort: medium
description: Revisa código na ótica de revisor sênior de MR, antes do merge. Use via /revisao ou quando pedir "revisa isso", "olha o que vai pro MR", "está pronto pra mergear?". Foca no que reprova MR na vida real — autorização/escopo faltando, mesma informação derivada de formas divergentes, código morto, timers/estado assíncrono, desvio dos padrões vizinhos. /revisao = não commitados, /revisao mr = branch vs base, /revisao <ponto> ou mr <numero> = diff específico via GitLab. Todo achado em linguagem simples.
argument-hint: "[commit | mr [base|numero] | <commit|branch|tag>]"
allowed-tools: Read Bash Grep Glob mcp__gitlab__*
---

# Revisão de MR

## Roteamento — leia só o que o argumento pede (validacoes pontuais + code-review genérico reaproveitado)

Modos definidos em `references/regras.md`. Cada modo chama agents com contexto limpo e **reaproveita** a skill `code-review` oficial do Claude (se instalada) como base genérica:

- `args` vazio, `commit` ou `local` → modo commit/local: `references/commit.md` → reaproveita code-review + `validacoes.md`
- `args` começa com `mr`/`branch`/`merge` sem número → modo mr/branch: `references/mr.md` → reaproveita code-review + `validacoes.md`
- `args` é `mr <numero>` → modo MR GitLab: `references/mr.md` + `references/regras.md` (permissão) → reaproveita code-review + `validacoes.md`
- `args` é SHA/tag/branch (`HEAD~N`, `main`, etc.) → modo ponto fixo: `references/processo.md` → reaproveita code-review + `validacoes.md`

A skill `revisao` sempre controla o retorno: filtra, reescreve em linguagem simples, ordena do mais crítico ao menor e formata via `formato-saida.md`. Não leia referências que o modo não pede. Em todos os modos, `references/regras.md` (permissão de uso único) vale.

## Arquivos de referência

- `references/regras.md` — **fonte da verdade** para modos e permissão de uso único — leia primeiro
- `references/commit.md` — modo commit/local: diff não commitado + agents
- `references/mr.md` — modo mr/branch e mr <numero>: diff via base ou API GitLab + agents
- `references/processo.md` — fluxo base (fixar ponto, ler arquivos, checklist, validar, agregar)
- `references/validacoes.md` — 15 validações genéricas para qualquer stack (inclui pipeline/config, migração, idioma, rodar testes e comentários só quando necessário)
- `references/formato-saida.md` — formato obrigatório do relatório
- `references/code-smells.md` — baseline complementar (usada dentro de validacoes #3 e #5)
- `references/context7.md` — quando e como usar o MCP context7
