# Referência — Atualizar MR

Ver `regras-gerais.md` (regra #1 — prévia + confirmação com opções antes de enviar).

---

## Passo 1 — Validar projeto, IID e o que mudar

Formato esperado: `atualizar <iid> <o que mudar>`. Extrair o número; o restante descreve a mudança (título, descrição, branch de destino, responsável, labels, rascunho, etc).

Se faltar IID:
> "Qual o número (IID) da MR a atualizar?"

Se o pedido não deixar claro o que mudar:
> "O que você quer alterar na MR? (título, descrição, branch de destino, responsável, labels...)"

## Passo 2 — Buscar estado atual

Consultar a MR (ver `ver.md`, Passo 2 — `mcp__gitlab__get_merge_request`) para saber os valores atuais e montar o diff "antes → depois".

## Passo 3 — Aplicar princípio de título/descrição do `criar.md`, quando for o caso

Se a mudança pedida é em título ou descrição, e o motivo é "a MR ganhou mais commits"/"mudei de abordagem": preferir remontar a partir dos commits atuais (mesmo princípio de `criar.md` — curto, direto, vem dos commits) em vez de escrever prosa nova do zero.

## Passo 4 — Exibir prévia (obrigatório)

```
MR !<iid> — <título atual>
Título       : <atual>              → <novo, se alterado>
Descrição    : <atual, resumido>    → <nova, se alterada>
Destino      : <atual>              → <novo, se alterado>
Responsável  : <atual>              → <novo, se alterado>
Labels       : <atuais>             → <novas, se alteradas>
```

> "Confirma a atualização? `[S] Sim` | `[N] Cancelar` | `[E] Ajustar`"

## Passo 5 — Executar

```
mcp__gitlab__update_merge_request(
  project_id        = "<projeto>",
  merge_request_iid = "<iid>",
  title             = "<novo título, se alterado>",       # omitir se não alterado
  description       = "<nova descrição, se alterada>",    # omitir se não alterado
  target_branch     = "<novo destino, se alterado>",      # omitir se não alterado
  assignee_ids      = [<id, se alterado>],                # omitir se não alterado
  labels            = ["<label>", ...]                    # omitir se não alterado
)
```

Confirmar ao usuário: "MR !<iid> atualizada."

## Notas

- Fechar/reabrir a MR usa o mesmo `update_merge_request` com `state_event="close"|"reopen"` — tratar como parte deste fluxo, com a mesma prévia/confirmação.
