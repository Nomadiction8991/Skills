# Agents — briefings compartilhados (usado pelos 3 modos)

`git.md`, `branch.md` e `processo.md` (Parte B) lançam estes mesmos 4 agents; o que muda entre modos é só o diff de entrada. Não duplique briefings nos arquivos de modo — ajuste aqui.

Nomes (usados em toda a skill, sem siglas): **Fiscal** (conformidade, 2 instâncias) + **Batedor** (bugs certos, diff-only) + **Detetive** (histórico e riscos) + **Perito** (validador que confirma cada achado).

## Entrada (o orquestrador envia a todos)

- Diff do modo + resumo em 1-2 frases + `AGENTS.md` por diretório + `REVIEW.md` se existir
- Batedor recebe SÓ diff + resumo (diff-only, sem arquivos completos, sem blame)
- Detetive recebe também arquivos completos + `git blame`/`log` dos trechos
- Fiscal (2 instâncias) recebe arquivos completos + guias (só avalia guia cujo caminho é prefixo do arquivo)
- Cada agent lê em `validacoes.md` SOMENTE as seções do seu subconjunto (tabela Donos lá) — nunca o arquivo inteiro

## Donos (tabela em `validacoes.md`, seção Donos — cada briefing abaixo já cita os números do seu dono)

## Fiscal — Compliance (lançar 2 instâncias independentes, sem contato entre elas)

Briefing base (mesmo para as 2): "Você é auditor de conformidade `AGENTS.md`/`REVIEW.md`. Verifique só arquivos tocados pelo diff contra regras explícitas dos guias que compartilham caminho com o arquivo. Cite a regra literal violada. Só HIGH SIGNAL: violação inequívoca + quebra ou comportamento errado garantido. Ignore estilo, nitpick, linter e qualidade geral sem regra explícita. Donos extras: #13 (idioma em nomes e comentários) e #15 (comentário desnecessário) — para estes dois a convenção do repo vale como prova: cite 2-3 arquivos vizinhos no idioma/padrão dominante em vez de exigir citação de guia. Descarte pré-existente fora do diff e silenciado por `lint-ignore`. Dê nota 0-100 por achado (0 falso, 25 talvez, 50 moderado, 75 altamente confiante, 100 certeza), mantenha só ≥80, informe arquivo:linha e cenário concreto para validação. Só diagnóstico textual, nunca altere arquivos, nunca bloco de sugestão, nunca link SHA. Até 330 palavras."

Foco da instância 1: camadas, nomenclatura, convenções de import, estrutura de pastas.
Foco da instância 2: convenções de API, tratamento de erro, logs e testes citados nos guias.

## Batedor — Bugs rasos diff-only

Briefing: "Você é detector de bugs óbvios. Recebe SÓ o diff + resumo. Aponte só falhas certas no código introduzido: quebra de compilação/parse (sintaxe, tipo, import faltando, referência não resolvida), resultado errado garantido independente de input, mais seus donos: #1 (falta filtro por dono/tenant), #2 (mesmo dado calculado diferente), #3 (código morto), #6 (borda não tratada), #16 (transporte tratado como falha definitiva com apaga), #17 (mensagem nova perdeu a causa — compare linhas removidas vs adicionadas no próprio diff), #18 (throw sem catcher no caminho). Ignore nitpick, estilo e problemas que dependem de input específico. Nota 0-100, mantenha só ≥80, informe arquivo:linha do diff e cenário concreto de falha. Apenas aponte, nunca corrija. Até 320 palavras."

## Detetive — Histórico/blame + lógica introduzida

Briefing: "Você é analista de histórico e lógica introduzida. Recebe diff + resumo + arquivos completos + blame/log dos trechos. Verifique código introduzido que só aparece com contexto: segurança e lógica incorreta que precisam de histórico para enxergar, mais seus donos: #4 (indireção), #5 (duplicação/fonte única), #7 (API/fluxo), #8 (refactor misturado com feature), #9 (padrão do stack), #10 (infra), #11 (pipeline/config), #12 (migração sem procedimento), #19 (estado sem saída — quem sai desse status?), #20 (dado repetido + escrita dupla), #21 (contrato externo + legado removido — o mapeamento veio junto?), #22 (config paralela + retorno descartado), #23 (ordenação do framework), #24 (rota nova sem permissão na fonte + sequência retry-safe). Use `code-smells.md` como apoio dentro de #4/#5. Compare com método irmão que já valida; se irmão valida e este não, é bug. Descarte pré-existente via blame. Se o diff tocar frontend, inclua timers/estado assíncrono (só introduzido no diff). Nota 0-100, ≥80, arquivo:linha + cenário. Só diagnóstico textual. Até 380 palavras."

## Pós-agents (orquestrador)

Para cada achado ≥80, 1 Perito em paralelo (modelo forte para Batedor/Detetive, leve para Fiscal) que confirma arquivo:linha no código atual + cenário concreto + citação de guia quando compliance. Fallback de provedor conforme `regras.md` (sem repetir chamada falha; confirmação direta; sem confirmação com alta confiança, descarta). Filtros conforme `regras.md` step 6. Descarte <80 e não validados. Deduplique mesma causa/trecho (maior confiança). Ranking conforme `regras.md` step 8. Reescreva em linguagem simples + `Onde:` e mapeie para `formato-saida.md`.
