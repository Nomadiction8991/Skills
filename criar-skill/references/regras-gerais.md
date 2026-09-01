# Regras Gerais — Criação de Skills nos Padrões do Marketplace

Estas regras são não negociáveis. Valem para criar do zero ou refatorar skill existente.

## 1. Nome e pasta

- Pasta: `plugins/fluxo-trabalho/skills/<nome>/` onde `<nome>` é `lowercase-hyphen`, até 64 chars, igual ao `name` do frontmatter.
- Um skill = uma pasta + `SKILL.md` dentro dela. Referências em `references/*.md`, templates em `templates/*.md` se houver.

## 2. Frontmatter de SKILL.md

```yaml
---
name: <nome-da-pasta>
model: haiku | sonnet | opus  # haiku para simples, sonnet para médio, opus para complexo
effort: low | medium | high
description: "Uma frase que diz O QUE faz E QUANDO usar. Comece com 'Use via /<nome> ou sempre que...' e inclua gatilhos em linguagem natural — mesmo sem barra. Front-load palavras que o usuário diria."
argument-hint: "[exemplos de args para autocomplete]"
allowed-tools: Read Bash Grep Glob Write Edit  # só o mínimo necessário; para MR/GitLab adicione mcp__gitlab__*
---
```

- `description` é obrigatória — sem ela a skill não aparece. Escreva em 3ª pessoa, curta, com "Use via /..."
- `allowed-tools` só com o necessário. Se precisar de GitLab/TomTicket, adicione `mcp__gitlab__*` / `mcp__tomticket__*`.

## 3. SKILL.md enxuto — só roteia e aponta

- **Não** duplique regras, checklist, formato ou processo no SKILL.md. O SKILL.md tem no máximo: título, 1 parágrafo de propósito, `## Roteamento` (por `args`) e `## Arquivos de referência` (lista com 1 linha por arquivo).
- Cada `args` aponta para 1–2 arquivos em `references/`. A IA só lê o que o modo pede — por isso separamos `commit.md` vs `mr.md`, `criar` vs `refatorar`.
- Se o fluxo precisa de agents, diga no SKILL.md: "cada modo lança agents com contexto limpo" e detalhe os agents no `references/*.md` do modo.

**Por que:** SKILL.md grande é lido toda vez, mesmo quando o usuário só quer um modo. Enxuto = menos tokens, menos alucinação, leitura seletiva.

## 4. Referências modulares

- `references/regras-gerais.md` — fonte da verdade (este arquivo)
- `references/fluxo.md` — passo a passo por modo (criar vs refatorar)
- `references/checklist.md` — checklist de validação antes de escrever
- `references/validacoes.md`, `code-smells.md` etc. — só se o modo precisa
- `templates/*.md` — templates de saída (SKILL.md, commit, MR)

Cada referência é auto-contida: diz o que fazer, como verificar e quando reprovar.

## 5. Contexto isolado via sub-agente (general-purpose)

- Quem monta a skill (estrutura, SKILL.md rascunho, referências) roda em sub-agente `general-purpose` com contexto limpo: só disco real (`Read`/`Glob`/`Bash`), `regras-gerais.md`, `fluxo.md` do modo e `templates/skill.md`. Nunca usa histórico da conversa.
- Sub-agente **nunca** chama `Write`/`Edit` nem tools de nuvem. Ele só devolve rascunho + árvore para o agente principal.
- Agente principal mostra a prévia, pede `[S]/[N]` e só então escreve.

**Por que:** evita skill que "parece pronta" mas nunca viu o disco; garante que a skill segue o estado real do repo.

## 6. Permissão de uso único (nunca automático)

- Tudo que cria/edita (`Write`/`Edit`) ou vai para nuvem exige confirmação explícita **naquela vez**. Mesmo que o usuário disse `S` antes, perguntar de novo no mesmo contexto.
- Fluxo: 1) mostrar prévia exata (árvore + SKILL.md), 2) perguntar `Posso criar/atualizar a skill <nome> em plugins/fluxo-trabalho/skills/<nome>? [S/N]`, 3) só após `S` escrever. Permissão expira após uso.
- Sem `S` = não escreve.

## 7. Linguagem e genericidade

- Escreva em PT-BR, frases curtas, voz ativa, linguagem simples — sem jargão preso a um stack.
- Skill deve funcionar em qualquer projeto/stack. Não amarre a um framework específico; se precisar exemplificar, marque como `ex.:`.
- Sem emojis, sem tabelas de nível com emoji, severidade em texto se precisar.

## 8. Versionamento

- Skill vive dentro do plugin `fluxo-trabalho`. Ao criar/refatorar skill, bump `version` em `plugins/fluxo-trabalho/.claude-plugin/plugin.json` + `.claude-plugin/marketplace.json` + `plugins/fluxo-trabalho/package.json` + `index.json` — é o sinal de atualização para o Claude.
