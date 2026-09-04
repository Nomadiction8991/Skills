# Regras Essenciais — fonte da verdade para modos e permissão

## Modos (resolva pelo `args`)

- vazio/`git` → modo git/local: `git diff` + `git diff --cached`
- `branch` ou `branch <base>` → modo branch: `git diff <base>...HEAD`, sempre com a branch atual em `HEAD`. A base é, nesta ordem: 2º token; `branch.<nome-atual>.base`; upstream configurado quando ele aponta para uma branch diferente da atual; ou a branch registrada no reflog como origem da criação. Não assumir `main`, `master` ou outra base por convenção; se a base não puder ser determinada com segurança, interromper e informar que é necessário usar `/revisao branch <base>`.
- SHA de commit ou tag passado diretamente (`HEAD~N`, SHA, `v1.2.0`, etc.) → modo ponto fixo: `git diff <ponto>...HEAD`

Detalhes de captura em `references/processo.md#1` e agents em `references/git.md` e `references/branch.md`.

## Scoring e validação — adaptação code-review

- Cada achado interno recebe confiança 0-100: 0-25 falso positivo/pré-existente, 26-50 detalhe menor não citado em AGENTS.md, 51-75 válido baixo impacto, 76-90 importante, 91-100 crítico. Só segue para validação se ≥80; relatório final nunca expõe número, só severidade textual `bloqueia` (91-100 ou quebra), `importante` (80-90 com impacto real) ou `menor` (80-90 baixo risco).
- Todo achado ≥80 passa por validação paralela: sub-agent confirma `arquivo:linha` no código atual + cenário concreto de falha. Sem confirmação, descarta. `arquivo:linha` é só para validação interna, nunca no relatório.
- Falsos positivos a descartar (traduzidos do oficial): pré-existente fora do diff; parece bug mas está correto; pedante que sênior não barraria; linter já pegaria; qualidade geral sem regra explícita em AGENTS.md; silenciado por `lint-ignore`. Nunca use bloco de sugestão aplicável nem link com SHA; `Como corrigir:` é só orientação textual.

## Antes de analisar

- Defina o escopo pelo modo escolhido: alterações locais (`git diff`/`git diff --cached`), branch atual contra a base (`git diff <base>...HEAD`) ou ponto SHA/tag (`git diff <ponto>...HEAD`). O escopo não pode ser ampliado durante a revisão.
- Leia `validacoes.md` (15 validações pontuais, incluindo pipeline, migração, idioma, rodar testes e comentários só quando necessário) além do checklist de 7 eixos e do code-review genérico reaproveitado (`processo.md#0`) — todo diff passa pelos três
- Identifique tecnologias no diff e use MCP `context7` (`context7.md`) se disponível; roda reinventada já está em `validacoes.md#4` e `#9`
- Entregue no formato de `formato-saida.md`. A revisão é estritamente diagnóstica e de somente leitura: nunca altera arquivos, nunca aplica correções no código e nunca commita. A skill `revisao` apenas reporta os problemas encontrados e controla o retorno do code-review genérico
- Use arquivos completos, busca global, histórico, código vizinho, documentação e testes somente para entender o escopo. Reporte apenas problemas introduzidos pelo diff ou diretamente no conteúdo revisado; descarte achados pré-existentes, não relacionados ou fora do diff.

## Regras rígidas — somente leitura e permissão explícita (nunca automático)

Esta skill é 100% de leitura e diagnóstico. Ela NUNCA altera, edita, commita ou corrige código diretamente:

- **Proibido editar arquivos ou aplicar correções:** Nenhuma ferramenta de escrita ou edição (`Write`, `Edit`, scripts de alteração via `Bash`, etc.) pode ser executada durante a revisão.
- **Sempre mostrar os problemas antes:** A revisão deve sempre listar todos os problemas encontrados (achados). O usuário é o único que decide se vai resolvê-los ou não posteriormente.
- **Resumo fiel ao estado atual:** O relatório e o `Resumo final` devem retratar os problemas encontrados no diff, jamais apresentar o código como "já corrigido".
- **Ações externas/nuvem exigem confirmação explícita do usuário:** Vale para: `git commit`, `git branch`, `git push`, criar/atualizar MR, postar comentário em MR, qualquer escrita via MCP GitLab. Embora criar branch/MR/commit não seja serviço desta skill, a regra vale se um dia for pedido.
- **Sem "S" explícito naquela pergunta = não executa.** Não usar "já autorizou antes" ou "está no mesmo contexto" como permissão.
- **Permissão é de uso único:** mesmo que o usuário disse `S` para postar no MR 12, para o MR 13 ou para commitar é preciso perguntar de novo.
- **Fluxo para nuvem:** 1) mostrar texto exato do que será enviado, 2) perguntar `Posso ...? [S/N]` e aguardar `S`, 3) só então executar. A permissão expira após o uso.
