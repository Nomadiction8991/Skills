# Regras Essenciais — fonte da verdade para modos e permissão

## Modos (resolva pelo `args`)

- vazio/`git` → modo git/local: `git diff` + `git diff --cached`
- `branch` ou `branch <base>` → modo branch: `git diff <base>...HEAD`, sempre com a branch atual em `HEAD`. A base é, nesta ordem: 2º token; `branch.<nome-atual>.base`; upstream configurado quando ele aponta para uma branch diferente da atual; ou a branch registrada no reflog como origem da criação. Não assumir `main`, `master` ou outra base por convenção; se a base não puder ser determinada com segurança, interromper e informar que é necessário usar `/revisao branch <base>`.
- SHA de commit ou tag passado diretamente (`HEAD~N`, SHA, `v1.2.0`, etc.) → modo ponto fixo: `git diff <ponto>...HEAD`

Detalhes de captura em `references/processo.md#1` e agents em `references/git.md` e `references/branch.md`.

## Técnica oficial `code-review` adaptada (extraída do plugin oficial)

Extraído de `anthropics/claude-code/plugins/code-review` (README + `code.claude.com/docs/en/code-review`). A `revisao` mantém seus 3 modos (`git`/`branch`/`SHA`, sem modo PR) mas reaproveita os steps 1-7 do `code-review`:

1. **Checar se revisão é necessária:** pule se diff vazio, trivial (só lockfile/gerado), ou já revisada. Não rode linter aqui — apenas descarte pós-scoring.
2. **Coletar guias `AGENTS.md` (+ `REVIEW.md` se existir):** liste `AGENTS.md` da raiz + cada pasta que contém arquivo tocado (ex.: `src/pedidos/svc.go` → `/AGENTS.md`, `/src/AGENTS.md`, `/src/pedidos/AGENTS.md`). Use `Glob` + `Read`. Cada agent só avalia regra cujo caminho é prefixo do arquivo. Se houver `REVIEW.md` na raiz, trate como instrução só-de-revisão (complementa `AGENTS.md` com severidade, caps de nit e checks específicos).
3. **Resumir mudanças:** gere 1-2 frases do que o diff faz (arquivos tocados, intenção). O resumo alimenta os 4 agents e evita alucinação.
4. **Lançar 4 agents paralelos independentes (general-purpose, só leitura):**
   - **A1 e A2 — AGENTS compliance (redundância):** auditam aderência a regras explícitas de `AGENTS.md`/`REVIEW.md`. Só apontam violação quando citam a regra exata. Dois agents reduzem falso negativo.
   - **B1 — Bugs rasos diff-only:** olha só o diff, sem contexto extra. Aponta só bug que quebra build ou dá resultado errado sempre (sintaxe, tipo, import faltando, referência não resolvida, lógica invertida). Ignora nitpick.
   - **B2 — Histórico/blame + lógica introduzida:** usa `git blame`/`git log`/`git show` para código introduzido. Busca segurança, lógica incorreta e divergência que só aparece com histórico.
5. **Scoring 0-100 por achado (interno, nunca no relatório):** 0 falso positivo, 25 talvez real, 50 moderado menor, 75 altamente confiante importante, 100 certeza crítica — mapeado em faixas 0-25 pré-existente/falso, 26-50 menor não citado, 51-75 válido baixo impacto, 76-90 importante, 91-100 bloqueia. Só mantém ≥80 para validação. Limiar padrão 80 (configurável: edite `regras.md` se o projeto quiser 70/85).
6. **Filtros de falsos positivos (antes e depois da validação):** descarte pré-existente fora do diff; parece bug mas está correto no contexto; pedante que sênior não barraria; linter já pegaria (não rodar linter); qualidade geral sem regra explícita em `AGENTS.md`/`REVIEW.md`; silenciado por `lint-ignore`/`eslint-disable`. Sem bloco de sugestão aplicável, sem link com SHA.
7. **Validação paralela (1 validador por achado ≥80):** sub-agent independente confirma `arquivo:linha` no código atual + cenário concreto de falha ("usuário B abre /pedido/17 → vê do A"). Sem confirmação com alta confiança, descarte. `arquivo:linha` só para validação interna, nunca no relatório.
8. **Deduplicação e ranking:** após validação, deduplique achados que falam do mesmo trecho/causa e ordene por severidade textual `bloqueia` (91-100 ou quebra) > `importante` (80-90 com impacto real) > `menor` (80-90 baixo risco). Limite a 5 numerados; extras agrupados como 1 `menor` extra.

`Como corrigir:` é só orientação textual, nunca bloco aplicável nem link com SHA (divergência proposital do oficial: esta skill não posta link com SHA no relatório).

Compatibilidade: o genérico pega bugs rasos + `AGENTS.md` compliance; `validacoes.md` (15) + 7 eixos pegam o que reprova na prática (autorização, divergência, código morto etc.). `processo.md#3` une ambos; `processo.md#3.5` continua rodando testes sempre.

## Scoring e validação — adaptação code-review

- Cada achado interno recebe confiança 0-100: 0-25 falso positivo/pré-existente, 26-50 detalhe menor não citado em AGENTS.md, 51-75 válido baixo impacto, 76-90 importante, 91-100 crítico. Só segue para validação se ≥80; relatório final nunca expõe número, só severidade textual `bloqueia` (91-100 ou quebra), `importante` (80-90 com impacto real) ou `menor` (80-90 baixo risco).
- Todo achado ≥80 passa por validação paralela: sub-agent confirma `arquivo:linha` no código atual + cenário concreto de falha. Sem confirmação, descarta. `arquivo:linha` é só para validação interna, nunca no relatório.
- Falsos positivos a descartar (traduzidos do oficial): pré-existente fora do diff; parece bug mas está correto; pedante que sênior não barraria; linter já pegaria; qualidade geral sem regra explícita em AGENTS.md; silenciado por `lint-ignore`. Nunca use bloco de sugestão aplicável nem link com SHA; `Como corrigir:` é só orientação textual.

## Antes de analisar

- Defina o escopo pelo modo escolhido: alterações locais (`git diff`/`git diff --cached`), branch atual contra a base (`git diff <base>...HEAD`) ou ponto SHA/tag (`git diff <ponto>...HEAD`). O escopo não pode ser ampliado durante a revisão.
- Resuma o diff em 1-2 frases antes de lançar agents (arquivos tocados + intenção).
- Leia `validacoes.md` (15 validações pontuais, incluindo pipeline, migração, idioma, rodar testes e comentários só quando necessário) além do checklist de 7 eixos e do code-review genérico reaproveitado (`processo.md#0`) — todo diff passa pelos três.
- Identifique tecnologias no diff e use MCP `context7` (`context7.md`) se disponível; roda reinventada já está em `validacoes.md#4` e `#9`.
- Entregue no formato de `formato-saida.md`. A revisão é estritamente diagnóstica e de somente leitura: nunca altera arquivos, nunca aplica correções no código e nunca commita. A skill `revisao` apenas reporta os problemas encontrados e controla o retorno do code-review genérico.
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
