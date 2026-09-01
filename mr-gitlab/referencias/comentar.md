# Referência — Comentar MR

Ver `regras-gerais.md` (regra #1 — prévia + confirmação com opções antes de enviar) e (regra #4 — Markdown, não HTML).

---

## Passo 1 — Validar projeto, IID e mensagem

Formato esperado: `comentar <iid> <mensagem>`. Extrair o primeiro token numérico como `merge_request_iid`; o restante é a mensagem. Projeto: inferir do repositório atual.

Se faltar IID:
> "Qual o número (IID) da MR?"

Se faltar mensagem:
> "Qual o conteúdo do comentário?"

## Passo 2 — Comentário simples vs. reply em thread

- **Comentário novo (top-level):** `mcp__gitlab__create_merge_request_note`.
- **Resposta dentro de uma discussion/thread existente** (usuário menciona "responde a discussão X" ou está resolvendo um comentário de review): `mcp__gitlab__create_merge_request_discussion_note`, que exige `discussion_id`. Se não tiver o `discussion_id`, buscar via `mcp__gitlab__get_merge_request_notes` e localizar a thread pelo contexto informado pelo usuário.

Se ambíguo, perguntar:
> "É um comentário novo ou resposta a uma discussão existente? `[1] Comentário novo` `[2] Responder discussão`"

## Passo 3 — Exibir prévia (obrigatório)

```
Comentário na MR !<iid>
[Comentário em Markdown]
```

> "Deseja enviar este comentário? `[S] Sim` | `[N] Cancelar` | `[E] Editar mensagem`"

## Passo 4 — Enviar

```
mcp__gitlab__create_merge_request_note(project_id="<projeto>", merge_request_iid="<iid>", body="<comentário>")
```
ou, para reply em thread:
```
mcp__gitlab__create_merge_request_discussion_note(project_id="<projeto>", merge_request_iid="<iid>", discussion_id="<id>", body="<comentário>")
```

Confirmar ao usuário: "Comentário adicionado na MR !<iid>."
