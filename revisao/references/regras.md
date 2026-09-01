# Regras Essenciais — fonte da verdade para modos e permissão

## Modos (resolva pelo `args`)

- vazio/`commit`/`local` → modo commit/local: `git diff` + `git diff --cached`
- `mr`/`branch`/`merge` sem número → modo mr/branch: `git diff <base>...HEAD` (base = 2º token ou `origin/main` → `main` → `master` → `develop`)
- `mr <numero>` → modo MR GitLab: diff via API (`get_merge_request`/`get_merge_request_diffs`)
- outro (`HEAD~N`, SHA, tag, branch) → modo ponto fixo: `git diff <ponto>...HEAD`

Detalhes de captura em `references/processo.md#1` e agents em `references/commit.md` / `references/mr.md`.

## Antes de analisar

- Leia `validacoes.md` (15 validações pontuais, incluindo pipeline, migração, idioma, rodar testes e comentários só quando necessário) além do checklist de 7 eixos e do code-review genérico reaproveitado (`processo.md#0`) — todo diff passa pelos três
- Identifique tecnologias no diff e use MCP `context7` (`context7.md`) se disponível; roda reinventada já está em `validacoes.md#4` e `#9`
- Entregue no formato de `formato-saida.md`. Não há hook automático — revisão é sempre manual e nunca commita/corrige sozinha. A skill `revisao` controla o retorno do code-review genérico

## Regras rígidas — permissão explícita (nunca automático)

Esta skill só revisa. Criação e edição ou qualquer coisa que vai para nuvem **exige confirmação explícita do usuário naquela vez**:

- Vale para: `git commit`, `git branch`, `git push`, criar/atualizar MR, postar comentário em MR, qualquer escrita via MCP GitLab. Embora criar branch/MR/commit não seja serviço desta skill, a regra vale se um dia for pedido.
- **Sem "S" explícito naquela pergunta = não executa.** Não usar "já autorizou antes" ou "está no mesmo contexto" como permissão.
- **Permissão é de uso único:** mesmo que o usuário disse `S` para postar no MR 12, para o MR 13 ou para commitar é preciso perguntar de novo.
- **Fluxo para nuvem:** 1) mostrar texto exato do que será enviado, 2) perguntar `Posso ...? [S/N]` e aguardar `S`, 3) só então executar. A permissão expira após o uso.
