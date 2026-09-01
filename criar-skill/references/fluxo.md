# Fluxo — Criar ou Refatorar Skill

## Modo criar (args é nome ou descrição)

### 0. Entender o pedido (se vago, entreviste)

Se `args` é vago ("skill que faz X") e falta alvo, gatilhos ou escopo, acione `fluxo-trabalho:entreviste-me` em rodada curta (1–2 perguntas) para fechar nome, gatilhos em linguagem natural e o que a skill NÃO faz. Não crie skill genérica sem entender o caso de uso.

### 1. Descobrir padrões reais (não pergunte, leia)

Lance sub-agente `general-purpose` para ler em contexto isolado:
- `plugins/fluxo-trabalho/skills/*/SKILL.md` (exemplos: `commit`, `revisao`, `chamado-tomticket`)
- `references/regras-gerais.md` + `templates/skill.md`
- Estrutura de pastas existente (`skills/<nome>/references/`, `templates/`)

O sub-agente retorna padrões observados (roteamento por args, SKILL.md enxuto, etc.) — não use sua memória.

### 2. Montar rascunho em contexto isolado

Ainda no sub-agente, monte:
- Árvore: `plugins/fluxo-trabalho/skills/<nome>/SKILL.md` + `references/regras-gerais.md` + `references/fluxo.md` (+ `checklist.md` se precisar) + `templates/*.md` se houver
- `SKILL.md` enxuto com frontmatter válido (name = pasta, description com "Use via /<nome> ou sempre que...", argument-hint, allowed-tools mínimo) + `## Roteamento` + `## Arquivos de referência`
- Referências com "o que fazer / como verificar / quando reprovar", sem duplicar o SKILL.md

O sub-agente **não** escreve — só devolve rascunho.

### 3. Validar (checklist)

No agente principal, valide o rascunho com `references/checklist.md` antes de mostrar prévia. Se reprovar, corrija no rascunho.

### 4. Prévia + permissão

Mostre no terminal:
- Árvore de arquivos
- Conteúdo de `SKILL.md` completo
- Lista de `references/*.md` que serão criados

Pergunte: `Posso criar a skill <nome> em plugins/fluxo-trabalho/skills/<nome>? [S/N]` — aguarde `S`.

### 5. Escrever e bump

Só após `S`, faça `Write` dos arquivos. Depois bump `version` nos 4 manifests (`plugin.json`, `marketplace.json`, `package.json`, `index.json`).

---

## Modo refatorar (args = refatorar <nome>)

### 1. Ler a skill existente por inteiro

- `plugins/fluxo-trabalho/skills/<nome>/SKILL.md` + todos os `references/*.md` + `templates/*.md` se houver
- `references/regras-gerais.md` + `references/checklist.md`

### 2. Diagnosticar em sub-agente

Lance sub-agente `general-purpose` com a skill atual + `regras-gerais.md` + `checklist.md`. Briefing: "Aponte onde a skill viola os padrões: SKILL.md inchado/duplicado, referências não modulares, falta de roteamento por args, falta de contexto isolado, falta de permissão de uso único, linguagem não genérica. Reporte por arquivo:linha, máximo 5 achados, linguagem simples."

### 3. Propor novo SKILL.md enxuto + referências modulares

No mesmo sub-agente, gere rascunho do novo `SKILL.md` (só roteamento + apontamentos) e lista de referências a ajustar/criar/remover. Não escreva.

### 4. Validar, prévia e permissão

Mesmo que modo criar (passos 3–5): checklist, prévia com diff do que muda, pergunta `Posso refatorar a skill <nome>? [S/N]`, só então `Write`/`Edit` + bump.
