# Context7 MCP — uso opcional

Este apêndice é opcional. A revisão funciona sem `context7`; use-o só se já estiver disponível no ambiente.

## Quando usar

Antes de revisar, identifique as tecnologias presentes no diff (framework, libs, SDKs, CLIs). Se o MCP `context7` estiver disponível nesta sessão, use-o para buscar documentação atualizada dessas tecnologias e validar padrões/bordas. Se não estiver, siga sem ele — não bloqueie a revisão.

## Como verificar (se quiser usar)

```bash
# lista MCPs disponíveis na sessão
# se `context7` aparecer, use-o; senão, ignore
```

Não instale automaticamente nem sobrescreva configurações sem ler/mesclar. Instalação global só se o usuário pedir explicitamente:

```bash
# exemplo opcional, só com permissão explícita do usuário
# claude mcp add --transport http --scope user context7 https://mcp.context7.com/mcp
```

Se o usuário tiver `CONTEXT7_API_KEY`, prefira header via JSON, mas sempre com `S` explícito antes de qualquer escrita de config.
