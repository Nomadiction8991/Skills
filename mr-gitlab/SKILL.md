---
name: mr-gitlab
description: "Consulta, comenta, aprova, cria ou atualiza Merge Requests (MRs) no GitLab. Use via /gitlab ou SEMPRE que a palavra 'mr' (isolada ou dentro da frase) aparecer se referindo a Merge Request do GitLab, mesmo sem sintaxe exata — ex: 'abre uma mr pra isso', 'o que tem na mr 123', 'comenta na mr que...', 'aprova a mr 45', 'lista as mrs abertas', 'muda o título da mr 12'. Também aciona por 'merge request' ou 'pull request do gitlab' por extenso. Usa o MCP do GitLab; se ele não estiver conectado, usa o glab (CLI do GitLab) quando estiver logado; só autoinstala o MCP se nenhum dos dois existir. Do NOT use for local git commits, pushes, or code review — GitLab MRs only."
model: haiku
effort: medium
argument-hint: "listar [filtro] | ver <id> | comentar <id> <mensagem> | aprovar <id> | aprovar <id> | criar <descrição> | atualizar <id> <o que mudar> | merge <id>"
allowed-tools: Read Bash mcp__gitlab__list_merge_requests mcp__gitlab__get_merge_request mcp__gitlab__get_merge_request_diffs mcp__gitlab__list_merge_request_changed_files mcp__gitlab__create_merge_request mcp__gitlab__update_merge_request mcp__gitlab__create_merge_request_note mcp__gitlab__create_merge_request_discussion_note mcp__gitlab__get_merge_request_notes mcp__gitlab__approve_merge_request mcp__gitlab__unapprove_merge_request mcp__gitlab__get_merge_request_approval_state mcp__gitlab__merge_merge_request
---

# Skill — GitLab (Merge Requests)

Você é um assistente que gerencia Merge Requests no GitLab via MCP ou, na falta dele, via `glab` (CLI do GitLab).

## Referências

Leia os arquivos relevantes antes de executar qualquer fluxo:

- **Detectar acesso (MCP ou glab) e autoinstalação (ler PRIMEIRO, sempre, antes de qualquer operação):** `references/install.md`
- **Fallback via glab (quando o MCP não está conectado):** `references/glab.md`
- **Regras gerais (não negociáveis, valem para toda operação):** `references/regras-gerais.md`
- **Listar MRs:** `references/listar.md`
- **Ver MR:** `references/ver.md`
- **Comentar MR:** `references/comentar.md`
- **Aprovar/Desaprovar MR:** `references/aprovar.md`
- **Criar MR:** `references/criar.md`
- **Atualizar MR:** `references/atualizar.md`
- **Merge MR:** `references/merge.md`
- **Template de criação:** `templates/mr.md`
- **ToolSearch / deferred tools:** `references/toolsearch.md`

## Ordem de execução obrigatória

1. **Sempre primeiro:** ler `references/install.md` e detectar a via: MCP `gitlab` conectado → MCP; senão `glab` logado → glab; senão autoinstalação.
2. Via MCP: ler `references/toolsearch.md` e carregar via `ToolSearch` os schemas das tools do fluxo. Via glab: ler `references/glab.md` e trocar cada tool `mcp__gitlab__*` do fluxo pelo comando equivalente da tabela.
3. Ler `references/regras-gerais.md` antes de qualquer ação de escrita (comentar, aprovar, criar, atualizar, merge).
4. Executar o fluxo pedido.

## Exemplo
Usuário: "lista minhas MRs abertas" → ler `references/install.md` + `references/toolsearch.md`, chamar `list_merge_requests` sem `project_id` (`state: opened`) e listar uma por linha.

## Troubleshooting
- MCP gitlab não conectado nesta sessão → checar `glab auth status`; se logado, usar `references/glab.md`. Só sem glab seguir a autoinstalação de `references/install.md` (exige nova sessão).
- `InputValidationError` ao chamar tool → fazer ToolSearch do schema antes (`references/toolsearch.md`).

## Roteamento

- `args` começa com "listar" ou "lista" → seguir `references/listar.md`
- `args` começa com "ver" → seguir `references/ver.md`; primeiro token numérico é o IID
- `args` começa com "comentar" → seguir `references/comentar.md`; primeiro token numérico é o IID, restante é a mensagem
- `args` começa com "aprovar" ou "desaprovar"/"reprovar" → seguir `references/aprovar.md`
- `args` começa com "atualizar" → seguir `references/atualizar.md`
- `args` começa com "merge" ou "mergear" → seguir `references/merge.md` — ação mais crítica, prévia + confirmação explícita obrigatórias
- `args` começa com "publicar"/"publica", ou menciona "escadinha"/"stack"/"pilha"/"uma MR por branch" → seguir `references/criar.md` (modo escadinha)
- `args` começa com "criar" → seguir `references/criar.md` (título/descrição vêm dos commits da branch, não são escritos do zero) — mostrar prévia completa via `templates/mr.md` antes de confirmar
- qualquer outro `args` (vazio, ambíguo ou sem número de MR quando necessário) → perguntar ao usuário o que falta (projeto? número da MR? qual conteúdo?) antes de chamar qualquer tool
