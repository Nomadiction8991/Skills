# Evals — como rodar (manual)

`evals.json` não roda sozinho: cada eval é conferido à mão numa sessão limpa. Passo a passo por eval (`id`):

1. Abra uma sessão nova no repo do `prompt` do eval (sem contexto da conversa atual).
2. Execute exatamente o `prompt` (ex.: `revisa a branch ...`).
3. Com o relatório na tela, marque cada assertion: `passed: true` + `evidence` com o trecho do relatório que prova (ex.: `"evidence": "achado 2 cita mensagem antiga vs nova"`).
4. Critérios de aprovação do eval: todas as assertions `true` E nenhuma regra de `regras.md` violada (escopo, sem teto, `Onde:` em todo achado, resumo fiel).
5. Se reprovar, anote o `id` + assertion que falhou e ajuste a skill antes de commitar a mudança na skill.

 Rode os 4 evals após qualquer mudança em `validacoes.md`, `agents.md`, `regras.md` ou `formato-saida.md`. Mudança só em `revisao-exigente.md` exige ao menos o eval 4 (gates/fatiamento).
