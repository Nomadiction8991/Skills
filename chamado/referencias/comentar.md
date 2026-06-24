# Referência — Comentar Chamado

Passo a passo completo para adicionar comentários/respostas em chamados no TomTicket.

---

## Fluxo

### Passo 1 — Validar Número e Mensagem

O formato esperado é `comentar <número> <mensagem>`.

Extrair o primeiro token numérico como número do chamado. O restante é a mensagem.

Se faltar número:
> "Qual o número do chamado?"

Se faltar mensagem:
> "Qual o conteúdo do comentário?"

### Passo 2 — Consultar o Chamado

Chamar `mcp__tomticket__ver_chamado(numero="<número>")` para confirmar que o chamado existe e exibir contexto ao usuário.

### Passo 3 — Melhorar a Mensagem

Com base no contexto do chamado (lido no Passo 2) e na mensagem bruta do usuário, reescrever o comentário de forma profissional e clara, seguindo as diretrizes de `referencias/ferramentas.md`. Incluir o rodapé de IA.

### Passo 4 — Exibir Prévia para Aprovação

Mostrar ao usuário o comentário melhorado e perguntar se deseja enviar:

```
Comentário no chamado #[número]

  <comentário melhorado>
```

> "Deseja enviar este comentário? `[S] Sim` | `[N] Cancelar` | `[E] Editar mensagem`"

### Passo 5 — Enviar

Chamar `mcp__tomticket__comentar_chamado(numero="<número>", mensagem="<comentário melhorado>")`.

Confirmar ao usuário: "Comentário adicionado ao chamado #[número] com sucesso."

---

## Notas

- Comentários são públicos (visíveis ao cliente) por padrão
- Seguir as diretrizes de formatação em `referencias/ferramentas.md`
- O rodapé de IA deve ser incluído no comentário
