# Referência — Ler Chamado

Passo a passo completo para consulta/exibição de chamados no TomTicket.

---

## Fluxo

### Passo 1 — Validar o Número

O formato esperado é `ler <número>`.

Extrair o primeiro token numérico após "ler". Se não houver número, perguntar:
> "Qual o número do chamado que deseja consultar?"

### Passo 2 — Consultar o Chamado

Chamar `mcp__tomticket__ver_chamado(numero="<número>")`.

### Passo 3 — Exibir os Dados ao Usuário

Após receber os dados, apresentar em formato legível:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CHAMADO #<número>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Título      : <título>
  Status      : <status>
  Prioridade  : <prioridade>
  Departamento: <departamento>
  Categoria   : <categoria>
  Cliente     : <cliente>
  Operador    : <operador>
  Criado em   : <data_criação>

━━━━━━━━━━━━━ DESCRIÇÃO ━━━━━━━━━━━━━━━

<corpo da mensagem>

━━━━━━━━━━━━━ HISTÓRICO ━━━━━━━━━━━━━━━

<listar os eventos/histórico disponíveis>

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Passo 4 — Perguntar Próximo Passo (opcional)

Após exibir, perguntar se o usuário deseja fazer algo com o chamado:
> "Deseja fazer algo neste chamado? `[E] Editar` | `[C] Comentar` | `[T] Transferir` | `[N] Não, apenas isso`"

- **E:** inicia fluxo de edição (ler `referencias/editar.md`)
- **C:** iniciar fluxo de comentário
- **T:** iniciar fluxo de transferência
- **N:** encerra

---

## Notas

- O histórico retornado pela API geralmente vem sem formatação (texto plano)
- Se houver muitos eventos no histórico, exibir apenas os 5-10 mais recentes e perguntar se deseja ver mais
- A ferramenta `ver_chamado` é apenas leitura — nenhuma alteração é feita
