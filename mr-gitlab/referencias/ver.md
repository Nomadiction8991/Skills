# Referência — Ver MR

Fluxo **somente leitura** — não precisa confirmação do usuário (regra 1 de `regras-gerais.md` só vale para escrita).

---

## Passo 1 — Validar projeto e IID

Formato esperado: `ver <iid>`. Extrair o primeiro token numérico como `merge_request_iid`.

Projeto: inferir do repositório atual (`git remote get-url origin`); se não houver, perguntar.

Se faltar o número:
> "Qual o número (IID) da MR?"

## Passo 2 — Consultar

```
mcp__gitlab__get_merge_request(project_id="<projeto>", merge_request_iid="<iid>", include_summaries=false)
```

Manter `include_summaries=false` por padrão (custo extra de chamadas) — só usar `true` se o usuário pedir detalhe de aprovação/deploy/commits explicitamente.

Se o usuário pedir diff/arquivos alterados, complementar com `mcp__gitlab__get_merge_request_diffs` ou `mcp__gitlab__list_merge_request_changed_files`.

## Passo 3 — Exibir

```
MR !<iid> — <título>
Estado       : <opened | closed | merged>
Origem       : <source_branch> → <target_branch>
Autor        : <autor>
Responsável  : <assignee | Sem responsável>
Aprovações   : <se consultado>

**Descrição**
<descrição>
```

## Passo 4 — Perguntar próximo passo (opcional)

> "Deseja fazer algo com esta MR? `[C] Comentar` | `[A] Aprovar` | `[U] Atualizar` | `[M] Merge` | `[N] Só isso`"

- **C** → `comentar.md` | **A** → `aprovar.md` | **U** → `atualizar.md` | **M** → `merge.md` | **N** → encerra
