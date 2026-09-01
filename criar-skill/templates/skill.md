---
name: <nome>
model: sonnet
effort: medium
description: Uma frase com O QUE faz E QUANDO usar. Ex.: "Faz X. Use via /<nome> ou sempre que o usuário disser '...', '...' — mesmo sem barra, em linguagem natural."
argument-hint: "[subcomando | <arg> | descreva o pedido]"
allowed-tools: Read Bash Grep Glob Write Edit
---

# <Título da Skill>

1 frase de propósito + quando chamar.

## Roteamento — leia só o que o argumento pede

- `args` começa com `...` → modo ...: `references/...md`
- `args` começa com `...` → modo ...: `references/...md`
- `args` vazio ou outro → `help.md` ou `references/fluxo.md`

Não leia referências que o modo não pede.

## Arquivos de referência

- `references/regras-gerais.md` — fonte da verdade
- `references/fluxo.md` — passo a passo por modo
- `references/checklist.md` — checklist antes de escrever
- `templates/<template>.md` — template de saída

## Regra de ouro

> Lance sub-agente `general-purpose` com contexto limpo (só disco real + `regras-gerais.md` + `fluxo.md` do modo + `templates/...`) que monta rascunho e **não** escreve. Agente principal só confirma `[S]/[N]` e escreve.
