---
name: revisao
model: opus
effort: medium
description: >-
  Revisa código na ótica de revisor sênior de branch, antes do merge. Modo estritamente diagnóstico (somente leitura: nunca altera arquivos nem aplica correções). Use via /revisao ou quando pedir "revisa isso", "olha o que vai pro MR", "está pronto pra mergear?". Foca no que reprova na vida real — autorização/escopo faltando, mesma informação derivada de formas divergentes, código morto, timers/estado assíncrono, desvio dos padrões vizinhos. /revisao ou /revisao git = alterações não commitadas, /revisao branch [base] = branch atual vs base/pai, /revisao <sha|tag> = diff desde um commit ou tag. Todo achado em linguagem simples.
argument-hint: "[git | branch [base] | <sha|tag>]"
allowed-tools: Read Bash Grep Glob mcp__gitlab__*
---

# Revisão de Branch

## Regra obrigatória: Somente Leitura e Diagnóstico (Nunca aplicar correções)

Esta skill é **estritamente diagnóstica e de somente leitura**. É terminantemente proibido alterar, editar ou criar arquivos para corrigir o código durante a revisão. O papel da skill é **sempre e exclusivamente mostrar os problemas encontrados primeiro**. O usuário é quem decidirá posteriormente se, quando e como vai resolvê-los. O relatório e o resumo final nunca devem apresentar os problemas como "já corrigidos".

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
