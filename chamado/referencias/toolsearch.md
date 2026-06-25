# Referência — ToolSearch (Ferramentas Diferidas)

As ferramentas MCP do TomTicket (`ver_chamado`, `comentar_chamado`, etc.) são **deferred tools** — aparecem na lista de ferramentas disponíveis, mas sem schema carregado. Tentar chamá-las diretamente sem carregar o schema resulta em `InputValidationError`.

## Por que isso acontece

O sistema precisa saber quais parâmetros cada ferramenta aceita. Esse schema não é carregado automaticamente — é necessário usar `ToolSearch` com `select:` para carregá-lo na sessão.

## Como carregar os schemas

```bash
ToolSearch(query: "select:mcp__tomticket__ver_chamado,mcp__tomticket__comentar_chamado,mcp__tomticket__listar_chamados,mcp__tomticket__criar_chamado,mcp__tomticket__editar_chamado,mcp__tomticket__buscar_cliente,mcp__tomticket__listar_operadores")
```

Isso carrega múltiplas ferramentas de uma vez. Depois disso, todas ficam disponíveis normalmente durante aquela sessão.

## Comportamento da Skill

> **A skill `/chamado` já faz isso automaticamente** no início do fluxo (antes de qualquer operação, ela executa o `ToolSearch` com `select:` para carregar os schemas necessários).

Se você estiver fora da skill (ex: conversa avulsa sem usar `/chamado`), precisará executar o `ToolSearch` manualmente antes de chamar qualquer ferramenta do TomTicket.
