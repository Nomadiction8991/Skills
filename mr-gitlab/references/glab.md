# Referência — Fallback via `glab` (CLI do GitLab)

Usar quando o MCP `gitlab` **não** estiver conectado nesta sessão, mas o `glab` estiver instalado e logado (ver `install.md`, passo 1). É o mesmo acesso ao GitLab, só que por linha de comando. Não pedir token, não instalar MCP, não abrir sessão nova — seguir direto.

## Regras

- Rodar sempre de dentro do repo (o `glab` descobre o projeto pelo remote). Fora do repo, ou para outro projeto, passar `-R <namespace/repo>` (path normalizado conforme `criar.md` → `Normalização do remote`).
- Nunca usar modo interativo: sempre `--yes` quando existir e passar título/descrição por flag ou arquivo. Nunca `--web`, nunca editor.
- Nunca exibir o token (`glab auth status` já mascara; não usar `--show-token`).
- Descrição com várias linhas: gravar num arquivo do scratchpad e usar `--description-file <arquivo>` (evita problema de aspas).
- Prévia + confirmação de `regras-gerais.md` continuam valendo igual ao MCP.
- Saída em JSON quando precisar de campos exatos: `-F json` (ou `--output json`).
- Se algum comando não existir na versão instalada, usar `glab api` com o endpoint REST equivalente.

## Mapeamento MCP → glab

| Operação | MCP | glab |
|---|---|---|
| Listar | `list_merge_requests` | `glab mr list` (`--assignee=@me`, `--author=@me`, `--reviewer=@me`, `--merged`, `--closed`, `--all`, `--search`, `-l <label>`, `-t <destino>`, `-s <origem>`); todos os projetos: `glab api "merge_requests?scope=assigned_to_me&state=opened"` |
| Ver | `get_merge_request` | `glab mr view <iid>` (`-c` inclui comentários) |
| Diff / arquivos | `get_merge_request_diffs` / `list_merge_request_changed_files` | `glab mr diff <iid>` (`--raw`) |
| Comentários | `get_merge_request_notes` | `glab mr view <iid> -c` ou `glab api projects/:id/merge_requests/<iid>/discussions` |
| Comentar | `create_merge_request_note` | `glab mr note <iid> -m "<texto>"` |
| Responder thread | `create_merge_request_discussion_note` | `glab api -X POST projects/:id/merge_requests/<iid>/discussions/<discussion_id>/notes -f body="<texto>"` |
| Aprovar / desaprovar | `approve_merge_request` / `unapprove_merge_request` | `glab mr approve <iid>` / `glab mr revoke <iid>` |
| Estado de aprovação | `get_merge_request_approval_state` | `glab api projects/:id/merge_requests/<iid>/approval_state` |
| Criar | `create_merge_request` | `glab mr create -s <origem> -b <destino> -t "<título>" --description-file <arquivo> [-a <username>] --push=false --yes` |
| Atualizar | `update_merge_request` | `glab mr update <iid> [-t "<título>"] [--description-file <arquivo>] [-a <username>] [--target-branch <destino>] --yes` |
| Merge | `merge_merge_request` | `glab mr merge <iid> --yes` (+ `--squash`, `--remove-source-branch` conforme confirmado) |
| Usuário logado | `GET /user` | `glab api user` |

`:id` no `glab api` é trocado automaticamente pelo projeto do repo atual.

## Diferenças em relação ao MCP

- `-a`/`--assignee` recebe **username**, não id numérico (não precisa resolver id).
- `glab mr create` sem `--push=false` pode fazer push sozinho — sempre passar `--push=false`; o push é feito antes, pelo fluxo de `criar.md`.
- Sem `--description-file`, descrição vazia fica `-d ""`.
