# Referência — ToolSearch (Ferramentas Diferidas)

As ferramentas MCP do GitLab (`mcp__gitlab__*`) são **deferred tools** — aparecem na lista de ferramentas disponíveis, mas sem schema carregado. Chamá-las diretamente sem carregar o schema resulta em `InputValidationError`.

## Como carregar os schemas

Carregar só as tools que o fluxo pedido realmente vai usar (não precisa carregar todas de uma vez):

```
ToolSearch(query: "select:mcp__gitlab__list_merge_requests,mcp__gitlab__get_merge_request,mcp__gitlab__create_merge_request_note,mcp__gitlab__create_merge_request_discussion_note,mcp__gitlab__approve_merge_request,mcp__gitlab__unapprove_merge_request,mcp__gitlab__get_merge_request_approval_state,mcp__gitlab__create_merge_request,mcp__gitlab__update_merge_request,mcp__gitlab__merge_merge_request,mcp__gitlab__get_merge_request_diffs,mcp__gitlab__list_merge_request_changed_files,mcp__gitlab__get_merge_request_notes")
```

Depois disso, as tools carregadas ficam disponíveis normalmente durante a sessão.

## Comportamento da Skill

A skill `/gitlab` deve fazer esse `ToolSearch` automaticamente logo após confirmar (via `install.md`) que o MCP está conectado, e antes de chamar qualquer tool de Merge Request.

## Pré-requisito

Este passo só funciona se o MCP `gitlab` já estiver **conectado nesta sessão** (ver `install.md`). Um MCP recém-adicionado via `claude mcp add` só aparece disponível numa sessão nova — `ToolSearch` não consegue carregar schema de um servidor que a sessão atual nunca conectou.
