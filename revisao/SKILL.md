---
name: revisao
model: opus
effort: medium
description: "Use via /revisao ou sempre que o usuário disser 'revisa isso', 'olha o que vai pro MR', 'está pronto pra mergear?' — mesmo sem barra, em linguagem natural. Revisa código na ótica de revisor sênior antes do merge em modo somente leitura."
argument-hint: "[git | branch [base] | <sha|tag>]"
allowed-tools: Read Bash Grep Glob mcp__gitlab__*
---

# Revisão de Branch

## Roteamento — leia só o que o argumento pede (validacoes pontuais + code-review genérico reaproveitado)

Modos definidos em `references/regras.md`. Cada modo chama agents com contexto limpo e **reaproveita** a skill `code-review` oficial do Claude (se instalada) como base genérica:

- `args` vazio ou `git` → modo git/local: `references/git.md` → reaproveita code-review + `validacoes.md`
- `args` começa com `branch` → modo branch: `references/branch.md` → branch atual vs base/pai explícita ou detectada, reaproveita code-review + `validacoes.md`
- `args` é um SHA de commit ou tag (`HEAD~N`, `v1.2.0`, etc.) → modo ponto fixo: `references/processo.md` → reaproveita code-review + `validacoes.md`

A skill `revisao` sempre controla o retorno: filtra, reescreve em linguagem simples, ordena do mais crítico ao menor e formata via `formato-saida.md`. Não leia referências que o modo não pede. Em todos os modos, `references/regras.md` (permissão de uso único) vale.

## Regra obrigatória de escopo

A revisão deve ficar restrita ao conteúdo do escopo selecionado pelo argumento e pelo diff correspondente. Arquivos completos, busca global, histórico, código vizinho, documentação e testes podem ser consultados apenas como contexto; não ampliam o escopo. Só reporte problemas introduzidos pelo diff ou diretamente no conteúdo revisado. Descarte problemas pré-existentes, não relacionados ou fora do diff.

## Arquivos de referência

- `references/regras.md` — **fonte da verdade** para modos e permissão de uso único — leia primeiro
- `references/git.md` — modo git/local: diff não commitado + agents
- `references/branch.md` — modo branch: diff da branch atual contra a base/pai + agents
- `references/processo.md` — fluxo base (fixar ponto, ler arquivos, checklist, validar, agregar)
- `references/validacoes.md` — 15 validações genéricas para qualquer stack (inclui pipeline/config, migração, idioma, rodar testes e comentários só quando necessário)
- `references/formato-saida.md` — formato obrigatório do relatório
- `references/code-smells.md` — baseline complementar (usada dentro de validacoes #3 e #5)
- `references/context7.md` — quando e como usar o MCP context7
