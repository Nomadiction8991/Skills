# Referência — Merge de MR

Ação mais crítica desta skill — ver `regras-gerais.md` (regra #1, item sobre `merge_merge_request`). Diferente de comentar/aprovar/atualizar, um merge não tem edição posterior trivial: exige confirmação explícita, sem exceção.

---

## Passo 1 — Validar projeto e IID

Formato esperado: `merge <iid>` ou `mergear <iid>`. Extrair o número. Projeto: inferir do repositório atual.

Se faltar IID:
> "Qual o número (IID) da MR a mergear?"

## Passo 2 — Consultar estado atual (obrigatório antes de mergear)

Consultar a MR (ver `ver.md`, Passo 2) para confirmar:
- Estado é `opened` (não mergear MR já fechada/mergeada).
- Pipeline (se houver) não está falhando — se estiver, avisar o usuário e pedir confirmação extra antes de prosseguir.
- Aprovações pendentes, se o projeto exigir (`get_merge_request_approval_state`, se necessário).

Se algo estiver fora do esperado (pipeline falhando, aprovações pendentes exigidas), **não seguir direto para o merge** — reportar o problema e perguntar se o usuário quer mesmo assim.

## Passo 3 — Exibir prévia e confirmar

```
MR !<iid> — <título>
Origem → Destino: <source_branch> → <target_branch>
Pipeline: <status, se houver>
Aprovações: <status, se relevante>
```

> "Confirma o merge da MR !<iid> para `<target_branch>`? `[S] Sim, mergear` | `[N] Cancelar`"

Nunca aceitar "pode mergear" solto como confirmação — sempre mostrar a prévia acima e esperar `[S]` explícito (ver regra #1 de `regras-gerais.md`).

## Passo 4 — Opções de merge (perguntar só se relevante ao contexto)

Se o usuário não especificar, usar os padrões do projeto (não forçar squash nem remoção de branch por conta própria):
- `squash`: só ativar se o usuário pedir.
- `remove_source_branch`: só ativar se o usuário pedir ou o padrão do projeto já fizer isso.
- `merge_when_pipeline_succeeds` / `auto_merge`: usar se o usuário pedir para mergear assim que o pipeline passar, em vez de mergear imediatamente.

## Passo 5 — Executar

```
mcp__gitlab__merge_merge_request(
  project_id            = "<projeto>",
  merge_request_iid     = "<iid>",
  squash                = <true|false, se pedido>,
  merge_when_pipeline_succeeds = <true|false, se pedido>
)
```

Confirmar ao usuário: "MR !<iid> mergeada em `<target_branch>`." (ou "Merge agendado para quando o pipeline passar", se `merge_when_pipeline_succeeds=true`)
