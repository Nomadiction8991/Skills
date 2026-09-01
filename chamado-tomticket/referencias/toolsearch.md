# Referência — ToolSearch (Ferramentas Diferidas)

As ferramentas MCP do TomTicket (`ver_chamado`, `comentar_chamado`, etc.) são **deferred tools** — aparecem na lista de ferramentas disponíveis, mas sem schema carregado. Tentar chamá-las diretamente sem carregar o schema resulta em `InputValidationError`.

## Por que isso acontece

O sistema precisa saber quais parâmetros cada ferramenta aceita. Esse schema não é carregado automaticamente — é necessário usar `ToolSearch` com `select:` para carregá-lo na sessão.

## Como carregar os schemas

```bash
ToolSearch(query: "select:mcp__tomticket__ver_chamado,mcp__tomticket__comentar_chamado,mcp__tomticket__criar_chamado,mcp__tomticket__editar_chamado,mcp__tomticket__transferir_chamado,mcp__tomticket__buscar_cliente,mcp__tomticket__listar_departamentos,mcp__tomticket__listar_categorias,mcp__tomticket__listar_operadores,mcp__tomticket__listar_status,mcp__tomticket__listar_chamados")
```

Isso carrega múltiplas ferramentas de uma vez. Depois disso, todas ficam disponíveis normalmente durante aquela sessão.

## Comportamento da Skill

> **A skill `/chamado` já faz isso automaticamente** no início do fluxo (antes de qualquer operação, ela executa o `ToolSearch` com `select:` para carregar os schemas necessários).

Se você estiver fora da skill (ex: conversa avulsa sem usar `/chamado`), precisará executar o `ToolSearch` manualmente antes de chamar qualquer ferramenta do TomTicket.

---

## Fallback via curl (quando as ferramentas MCP falham com erro de login)

Se as chamadas via MCP (`buscar_cliente`, `listar_operadores`, `criar_chamado`, etc.) retornarem erro do tipo "Por favor, efetue login" mesmo com o servidor configurado corretamente, isso indica um problema de sessão do lado do transporte MCP dentro do Claude Code — **não** das credenciais em si. Nesse caso, é possível contornar chamando o mesmo servidor diretamente via HTTP/JSON-RPC com `curl`, usando as credenciais já configuradas para o MCP server `tomticket` (headers `X-TomTicket-Conta`, `X-TomTicket-Email`, `X-TomTicket-Senha`, presentes na config do MCP — não hardcodear, não exibir nem repetir esses valores na conversa).

### Como funciona

1. **URL:** a mesma do MCP server, **com barra final** (`.../mcp/`) — sem a barra, o servidor responde `307 Temporary Redirect`.
2. **Protocolo:** JSON-RPC 2.0 via POST, com `Content-Type: application/json` e `Accept: application/json, text/event-stream`.
3. **Sem sessão/cookie:** o servidor aceita `tools/call` diretamente, sem precisar de `initialize` nem manter sessão — basta enviar os headers de autenticação em toda requisição.

### Descobrir/ler as credenciais sem exibi-las

As credenciais já estão configuradas no MCP server (`~/.claude.json`, escopo `user` ou `project`, seção `mcpServers.tomticket.headers`). Leia o arquivo internamente para montar os headers do `curl`, mas **nunca imprima o valor de `X-TomTicket-Senha` nem o `X-TomTicket-Email` na resposta ao usuário** — só use os valores dentro do comando `curl` executado via Bash.

**Referência fixa (não sensível):**
- URL do MCP server: `http://angela.ellotecnologia.com:8082/mcp`
- Conta: `ellotecnologia`

O e-mail e a senha (`X-TomTicket-Email` / `X-TomTicket-Senha`) variam conforme o operador logado — sempre ler do config, nunca hardcodear ou exibir.

### Chamar uma tool

```bash
curl -s -X POST "<url-do-mcp-server-com-barra-final>" \
  -H "X-TomTicket-Conta: <valor da config>" \
  -H "X-TomTicket-Email: <valor da config>" \
  -H "X-TomTicket-Senha: <valor da config>" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"<nome_da_tool>","arguments":{...}}}' \
  --max-time 20
```

A resposta vem no formato SSE (`event: message` + `data: {...}`); o JSON útil está no campo `data`.

### Payloads maiores (ex: `criar_chamado` com HTML longo)

Escrever o corpo JSON-RPC em um arquivo (no diretório de scratchpad da sessão) e usar `--data @arquivo.json` em vez de montar o JSON inline no shell — evita problemas de escaping com aspas, acentos e HTML.

### Quando usar

Só recorrer a esse fallback se as tools MCP falharem repetidamente com erro de login/sessão. Preferir sempre as tools MCP nativas primeiro.
