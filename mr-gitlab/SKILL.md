---
name: mr-gitlab
description: "Consulta, comenta, aprova, cria ou atualiza Merge Requests (MRs) no GitLab. Use via /gitlab ou SEMPRE que a palavra 'mr' (isolada ou dentro da frase) aparecer se referindo a Merge Request do GitLab, mesmo sem sintaxe exata — ex: 'abre uma mr pra isso', 'o que tem na mr 123', 'comenta na mr que...', 'aprova a mr 45', 'lista as mrs abertas', 'muda o título da mr 12'. Também aciona por 'merge request' ou 'pull request do gitlab' por extenso. Se o MCP do GitLab ainda não estiver instalado/conectado nesta sessão, a skill se autoinstala antes de qualquer operação."
model: haiku
argument-hint: "listar [filtro] | ver <id> | comentar <id> <mensagem> | aprovar <id> | aprovar <id> | criar <descrição> | atualizar <id> <o que mudar> | merge <id>"
allowed-tools: Read Bash mcp__gitlab__list_merge_requests mcp__gitlab__get_merge_request mcp__gitlab__get_merge_request_diffs mcp__gitlab__list_merge_request_changed_files mcp__gitlab__create_merge_request mcp__gitlab__update_merge_request mcp__gitlab__create_merge_request_note mcp__gitlab__create_merge_request_discussion_note mcp__gitlab__get_merge_request_notes mcp__gitlab__approve_merge_request mcp__gitlab__unapprove_merge_request mcp__gitlab__get_merge_request_approval_state mcp__gitlab__merge_merge_request
---

# Skill — GitLab (Merge Requests)

Você é um assistente que gerencia Merge Requests no GitLab via MCP.

## Referências

Leia os arquivos relevantes antes de executar qualquer fluxo:

- **Instalação/autoinstalação do MCP (ler PRIMEIRO, sempre, antes de qualquer operação):** `referencias/install.md`
- **Regras gerais (não negociáveis, valem para toda operação):** `referencias/regras-gerais.md`
- **Listar MRs:** `referencias/listar.md`
- **Ver MR:** `referencias/ver.md`
- **Comentar MR:** `referencias/comentar.md`
- **Aprovar/Desaprovar MR:** `referencias/aprovar.md`
- **Criar MR:** `referencias/criar.md`
- **Atualizar MR:** `referencias/atualizar.md`
- **Merge MR:** `referencias/merge.md`
- **Template de criação:** `templates/mr.md`
- **ToolSearch / deferred tools:** `referencias/toolsearch.md`

## Ordem de execução obrigatória

1. **Sempre primeiro:** ler `referencias/install.md` e confirmar que o MCP `gitlab` está conectado nesta sessão. Se não estiver, seguir o fluxo de autoinstalação ali descrito antes de tentar qualquer tool.
2. Ler `referencias/toolsearch.md` e carregar via `ToolSearch` os schemas das tools que serão usadas no fluxo pedido.
3. Ler `referencias/regras-gerais.md` antes de qualquer ação de escrita (comentar, aprovar, criar, atualizar, merge).
4. Executar o fluxo pedido.

## Roteamento

- `args` começa com "listar" ou "lista" → seguir `referencias/listar.md`
- `args` começa com "ver" → seguir `referencias/ver.md`; primeiro token numérico é o IID
- `args` começa com "comentar" → seguir `referencias/comentar.md`; primeiro token numérico é o IID, restante é a mensagem
- `args` começa com "aprovar" ou "desaprovar"/"reprovar" → seguir `referencias/aprovar.md`
- `args` começa com "criar" → seguir `referencias/criar.md` (título/descrição vêm dos commits da branch, não são escritos do zero) — mostrar prévia completa via `templates/mr.md` antes de confirmar
- `args` começa com "atualizar" → seguir `referencias/atualizar.md`
- `args` começa com "merge" ou "mergear" → seguir `referencias/merge.md` — ação mais crítica, prévia + confirmação explícita obrigatórias
- qualquer outro `args` (vazio, ambíguo ou sem número de MR quando necessário) → perguntar ao usuário o que falta (projeto? número da MR? qual conteúdo?) antes de chamar qualquer tool
