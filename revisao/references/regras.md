# Regras Essenciais — fonte da verdade para modos e permissão

## Modos (resolva pelo `args`)

- vazio/`git` → modo git/local: `git diff` + `git diff --cached`
- `branch` ou `branch <base>` → modo branch: `git diff <base>...HEAD`, sempre com a branch atual em `HEAD`. A base é, nesta ordem: 2º token; `branch.<nome-atual>.base`; upstream configurado quando ele aponta para uma branch diferente da atual; ou a branch registrada no reflog como origem da criação. Não assumir `main`, `master` ou outra base por convenção; se a base não puder ser determinada com segurança, interromper e informar que é necessário usar `/revisao branch <base>`.
- SHA de commit ou tag passado diretamente (`HEAD~N`, SHA, `v1.2.0`, etc.) → modo ponto fixo: `git diff <ponto>...HEAD`

Detalhes de captura em `references/processo.md#1` e agents em `references/git.md` e `references/branch.md`.

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
