---
name: criar-skill
model: sonnet
effort: medium
description: Cria ou refatora skills seguindo os padrões deste marketplace (fluxo-trabalho). Use via /criar-skill ou sempre que o usuário disser "crie uma skill", "nova skill", "refatore a skill", "padronize a skill" — mesmo sem barra, em linguagem natural. Garante SKILL.md enxuto com roteamento, referências modulares, contexto isolado via sub-agente, permissão de uso único e linguagem simples.
argument-hint: "[nome | refatorar <nome> | descreva a skill desejada]"
allowed-tools: Read Bash Grep Glob Write Edit
---

# Criar Skill nos Padrões do Marketplace

Cria ou refatora skills dentro de `plugins/fluxo-trabalho/skills/<nome>/` seguindo exatamente os padrões que este marketplace usa — para que a IA só leia o que precisa, trabalhe com contexto limpo e nunca aja sem permissão explícita.

## Regra de ouro: por que seguimos estes padrões

- **SKILL.md enxuto + referências modulares:** a IA não precisa ler tudo. `SKILL.md` só roteia por `args` e aponta para `references/*.md`; cada modo lê só seu arquivo. Sem isso, a skill consome contexto à toa e alucina.
- **Contexto isolado via sub-agente:** quem monta a skill (scaffold, prévia) roda em `general-purpose` com só o estado real do repo (arquivos existentes, git), nunca com o histórico da conversa. O agente principal só confirma e escreve. Evita "skill que parece certa mas nunca foi validada no disco".
- **Permissão de uso único:** tudo que cria/edita ou vai para nuvem exige `S` explícito naquela vez — mesmo no mesmo contexto, perguntar de novo. Evita criação acidental de skill no lugar errado.
- **Linguagem simples e genérica:** skill funciona em qualquer projeto/stack, sem jargão preso a um domínio.
- **Roteamento por args:** primeiro token decide o fluxo; cada fluxo tem seu `references/*.md` e seu `templates/*.md`. Sem roteamento, a skill vira um bloco gigante que a IA tenta adivinhar.

> **Regra:** lance um sub-agente (`general-purpose`) para montar a estrutura/rascunho da skill em contexto isolado — o prompt do sub-agente inclui `references/regras-gerais.md`, o `references/fluxo.md` do modo e `templates/skill.md`, e a instrução explícita de nunca usar o histórico da conversa, só o disco real. O sub-agente **nunca** escreve arquivos; ele só devolve a árvore + SKILL.md + referências em rascunho para esta skill. A confirmação `[S]/[N]` e o `Write` sempre ficam aqui, no agente principal.

## Referências

Leia só o que o modo pedir:

- **Regras gerais (não negociáveis):** `references/regras-gerais.md` — nome/frontmatter, estrutura de pastas, SKILL.md enxuto, referências modulares, agents com contexto limpo, permissão, linguagem
- **Fluxo criar nova skill:** `references/fluxo.md` (criar)
- **Fluxo refatorar/padronizar skill existente:** `references/fluxo.md` (refatorar)
- **Checklist de validação:** `references/checklist.md` — valida se a skill segue os padrões antes de escrever
- **Template SKILL.md enxuto:** `templates/skill.md`
- **Ajuda:** `help.md`

## Roteamento

- `args` começa com `refatorar`, `padronizar`, `revisar` ou `corrigir` + `<nome>` → **modo refatorar** (ler `references/fluxo.md` seção refatorar + `references/regras-gerais.md` + `references/checklist.md`)
- `args` é um nome (`minha-skill`, `criar-skill`) ou descrição livre ("skill que faz X") → **modo criar** (ler `references/fluxo.md` seção criar + `references/regras-gerais.md`)
- `args` vazio ou dúvida → ler `help.md` e perguntar qual skill criar/refatorar e o que ela deve fazer

Em todos os modos, `references/regras-gerais.md` vale primeiro.
