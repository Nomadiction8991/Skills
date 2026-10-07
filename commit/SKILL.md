---
name: commit
description: "Avalia pendentes, separa em branches/MRs empilhadas e cria commits conventional commits. Use via /commit ou sempre que o usuário falar sobre commit/comitar, separar mudanças em branches, dividir MR, empilhar branches ou 1 branch 1 MR. Do NOT use for creating MRs, reviewing code, or pushing branches."
model: haiku
effort: low
argument-hint: "[mensagem]"
allowed-tools: Read Bash Grep Glob
---

# Commit + Separação em MRs

Avalia os pendentes, propõe a divisão em branches/MRs empilhadas (ideal 1 branch = 1 MR = 1 commit) e cria o commit bem formatado: $ARGUMENTS

## Regra de ouro: contexto isolado

A mensagem do commit é montada **somente** a partir do estado real do repositório — `git status`, `git diff`, `git log` e a leitura dos arquivos alterados. **Nunca** usar histórico da conversa, resumos do chat ou descrições do usuário como fonte da mensagem: se o diff não confirma, não entra no commit.

> **Regra:** as etapas de avaliação (Passo 2.5) e montagem (Passo 4) lançam sub-agentes (`Agent`, tipo `general-purpose`) em contexto isolado a partir do git real — nunca do histórico da conversa. Detalhe de entradas e proibições em `references/fluxo.md` (Passo 2.5 e Passo 4). A confirmação `[S]/[N]` e o `git commit` em si sempre ficam aqui, no agente principal (Passo 5 de `references/fluxo.md`).

## Estado Atual do Repositório

- Status Git: !`git status --porcelain`
- Branch atual: !`git branch --show-current`
- Alterações em staging: !`git diff --cached --stat`
- Alterações não em staging: !`git diff --stat`
- Commits recentes: !`git log --oneline -5`

## Exemplo
Usuário alterou 3 arquivos de um fix e pede commit → 1 commit conventional com subject + corpo em linguagem simples.

## Troubleshooting
- Mudanças lógicas distintas no diff → ir ao Passo 2.5 (separar em MRs) antes de montar a mensagem.
- Projeto Ello estando na base (main/master) → criar branch antes, nunca commitar direto.

## Referências

- **Regras gerais (não negociáveis, inclui limites de tamanho):** `references/regras-gerais.md`
- **Fluxo do comando (passo a passo, inclui detecção de projeto Ello):** `references/fluxo.md`
- **Especificação Conventional Commits:** `references/conventional-commits.md`
- **Variante Ello (projetos do Ello ERP):** `references/ello.md` + `templates/ello-commit.md`
- **Template de mensagem:** `templates/commit.md`
- **Template do plano de MRs (grafo de branches):** `templates/mr-plan.md`
- **Ajuda:** `help.md`
