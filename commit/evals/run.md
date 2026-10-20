# Evals — como rodar (manual)

`evals.json` não roda sozinho: cada eval é conferido à mão numa sessão limpa. Passo a passo por eval (`id`):

1. Monte um repo de teste com o cenário do `prompt` (pendentes reais no git, sem contexto da conversa atual).
2. Execute exatamente o `prompt`.
3. Com o resultado na tela, marque cada assertion: `passed: true` + `evidence` com o trecho que prova (ex.: `"evidence": "plano com grafo + 2 seções, sem branch criada antes do S"`).
4. Critérios de aprovação: todas as assertions `true` E nenhuma regra de `regras-gerais.md` violada (mensagem do diff, confirmação antes do commit, sem amend em commit enviado, sem branch sem [S]).
5. Se reprovar, anote o `id` + assertion que falhou e ajuste a skill antes de commitar a mudança na skill.

 Rode os 3 evals após qualquer mudança em `fluxo.md`, `regras-gerais.md`, `particao-cirurgica.md` ou templates de mensagem. Mudança só em `ello.md`/Ello exige ao menos um cenário Ello manual (subject sem tipo + pergunta de changelog).
