# Referência — Aprovar / Desaprovar MR

Ver `regras-gerais.md` (regra #1 — prévia + confirmação com opções antes de enviar).

---

## Passo 1 — Validar projeto e IID

Formato esperado: `aprovar <iid>` ou `desaprovar/reprovar <iid>`. Extrair o número. Projeto: inferir do repositório atual.

Se faltar IID:
> "Qual o número (IID) da MR a aprovar/desaprovar?"

## Passo 2 — Confirmar

Aprovação/desaprovação não tem "conteúdo" pra revisar como um comentário, mas ainda exige confirmação explícita (regra #1) — mostrar o que vai acontecer antes de agir:

> "Confirma aprovar a MR !<iid> (<título>)?" `[S] Sim` | `[N] Cancelar`

(trocar "aprovar" por "desaprovar" conforme o pedido)

## Passo 3 — Executar

```
mcp__gitlab__approve_merge_request(project_id="<projeto>", merge_request_iid="<iid>")
```
ou
```
mcp__gitlab__unapprove_merge_request(project_id="<projeto>", merge_request_iid="<iid>")
```

Confirmar ao usuário: "MR !<iid> aprovada." / "Aprovação removida da MR !<iid>."

## Notas

- `approve_merge_request` aceita `sha` opcional (HEAD esperado) — só usar se o usuário quiser garantir que está aprovando uma versão específica da MR (evita aprovar acidentalmente um push posterior).
