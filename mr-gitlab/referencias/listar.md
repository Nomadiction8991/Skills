# Referência — Listar MRs

Fluxo **somente leitura** — não precisa confirmação do usuário antes de executar (regra 1 de `regras-gerais.md` só vale para escrita).

---

## Passo 1 — Entender o filtro pedido

`mcp__gitlab__list_merge_requests` aceita, entre outros: `project_id` (se ausente, lista de todos os projetos que o usuário tem acesso), `assignee_username`, `author_username`, `reviewer_username`, `state` (`opened`/`closed`/`merged`/`all`), `search`, `labels`, `target_branch`, `source_branch`.

Se o usuário não informar projeto, tentar inferir do repositório atual:
```bash
git remote get-url origin
```
Se não houver repositório aberto e o usuário também não informar projeto, perguntar.

## Passo 2 — Buscar

Chamar `mcp__gitlab__list_merge_requests` com os filtros identificados. Sem filtro de `state`, usar `opened` como padrão (o mais útil no dia a dia) — avisar isso ao usuário se ele não tiver pedido explicitamente MRs fechadas/mergeadas.

## Passo 3 — Apresentar

Lista direta, sem formatação pesada — um item por linha:

```
!<iid> [<estado>] <título> — <origem> → <destino> (<autor>)
```

Se vier muita coisa, mostrar as mais recentes primeiro e perguntar se quer ver mais.

## Passo 4 — Oferecer aprofundar

Depois de mostrar a lista, perguntar se o usuário quer detalhes de alguma MR específica:
> "Quer que eu detalhe alguma dessas? Me passa o número (`!<iid>`)."

Se sim, seguir para `ver.md`.
