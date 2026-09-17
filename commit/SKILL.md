---
name: commit
description: "Avalia pendentes, separa em branches/MRs empilhadas e cria commits conventional commits. Use via /commit ou sempre que o usuário falar sobre commit/comitar, separar mudanças em branches, dividir MR, empilhar branches ou 1 branch 1 MR."
model: haiku
---

# Commit + Separação em MRs

Avalia os pendentes, propõe a divisão em branches/MRs empilhadas (ideal 1 branch = 1 MR = 1 commit) e cria o commit bem formatado: $ARGUMENTS

## Regra de ouro: contexto isolado

A mensagem do commit é montada **somente** a partir do estado real do repositório — `git status`, `git diff`, `git log` e a leitura dos arquivos alterados. **Nunca** usar histórico da conversa, resumos do chat ou descrições do usuário como fonte da mensagem: se o diff não confirma, não entra no commit.

> **Regra:** as etapas de avaliação (Passo 2.5) e montagem (Passo 4) lançam sub-agentes (`Agent`, tipo `general-purpose`) em contexto isolado a partir do git real — nunca do histórico da conversa. Detalhe de entradas e proibições em `referencias/fluxo.md` (Passo 2.5 e Passo 4). A confirmação `[S]/[N]` e o `git commit` em si sempre ficam aqui, no agente principal (Passo 5 de `referencias/fluxo.md`).

## Estado Atual do Repositório

- Status Git: !`git status --porcelain`
- Branch atual: !`git branch --show-current`
- Alterações em staging: !`git diff --cached --stat`
- Alterações não em staging: !`git diff --stat`
- Commits recentes: !`git log --oneline -5`

## Referências

- **Regras gerais (não negociáveis, inclui limites de tamanho):** `referencias/regras-gerais.md`
- **Fluxo do comando (passo a passo, inclui detecção de projeto Ello):** `referencias/fluxo.md`
- **Especificação Conventional Commits:** `referencias/conventional-commits.md`
- **Variante Ello (projetos do Ello ERP):** `referencias/ello.md` + `templates/ello-commit.md`
- **Template de mensagem:** `templates/commit.md`
- **Template do plano de MRs (grafo de branches):** `templates/mr-plan.md`
- **Ajuda:** `help.md`
