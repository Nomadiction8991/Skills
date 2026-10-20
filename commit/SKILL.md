---
name: commit
description: "Avalia pendentes, separa em branches/MRs empilhadas e cria commits conventional commits. Use via /commit ou sempre que o usuário falar sobre commit/comitar, separar mudanças em branches, dividir MR, empilhar branches ou 1 branch 1 MR. Do NOT use for creating MRs, reviewing code, or pushing branches."
model: haiku
effort: low
argument-hint: "[mensagem | separar]"
allowed-tools: Read Bash Grep Glob
---

# Commit + Separação em MRs

Avalia os pendentes, propõe a divisão em branches/MRs empilhadas (ideal 1 branch = 1 MR = 1 commit) e cria o commit bem formatado: $ARGUMENTS

Mensagem montada **somente** do git real (`git status`, `git diff`, `git log` + leitura dos arquivos) — nunca do histórico da conversa. Avaliação (2.5) e montagem (Passo 4) rodam em sub-agente isolado; confirmação `[S]/[N]` e `git commit` ficam no agente principal.

## Roteamento — leia só o que o argumento pede

- `args` menciona separar/dividir/empilhar, ou diff com frentes distintas → ir direto ao Passo 2.5 de `references/fluxo.md`
- `args` vazio ou instrução sobre a mensagem → fluxo completo (`references/fluxo.md`)
- Projeto Ello (detectado no Passo 0) → mensagem pela variante `references/ello.md`, resto pelo fluxo geral

## Estado Atual do Repositório

- Status Git: !`git status --porcelain`
- Branch atual: !`git branch --show-current`
- Alterações em staging: !`git diff --cached --stat`
- Alterações não em staging: !`git diff --stat`
- Commits recentes: !`git log --oneline -5`

## Exemplo
Usuário alterou 3 arquivos de um fix e pede commit → 1 commit conventional com subject + corpo em linguagem simples.

## Troubleshooting
- Arquivo compartilhado por mais de uma frente ou teste fora da pasta do código → partição cirúrgica (`references/particao-cirurgica.md`) no Passo 2.5.
- Projeto Ello estando na base (main/master) → criar branch antes, nunca commitar direto.

## Referências

- **Regras gerais (não negociáveis, inclui limites de tamanho):** `references/regras-gerais.md`
- **Fluxo do comando (passo a passo, inclui detecção de projeto Ello):** `references/fluxo.md`
- **Partição cirúrgica (staging parcial, arquivos compartilhados, régua estrita e rascunhos):** `references/particao-cirurgica.md`
- **Especificação Conventional Commits:** `references/conventional-commits.md`
- **Variante Ello (projetos do Ello ERP):** `references/ello.md` + `templates/ello-commit.md`
- **Template de mensagem:** `templates/commit.md`
- **Template do plano de MRs (grafo de branches):** `templates/mr-plan.md`
- **Ajuda:** `help.md`
- evals/evals.json — evals (prompt → saída esperada + assertions); `evals/run.md` — como rodar
