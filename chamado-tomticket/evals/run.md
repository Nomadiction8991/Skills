# Evals — como rodar (manual)

`evals.json` não roda sozinho: cada eval é conferido à mão numa sessão limpa. Passo a passo por eval (`id`):

1. Execute exatamente o `prompt` — **pare no [S]/[N] e responda N**: nenhum eval cria, edita, comenta ou transfere de verdade (só prévia e leitura).
2. Com o resultado na tela, marque cada assertion: `passed: true` + `evidence` com o trecho que prova.
3. Critérios de aprovação: todas as assertions `true` E nenhuma regra de `regras-gerais.md` violada (confirmação antes de escrita, HTML real no payload, problema sem solução no corpo).
4. Se reprovar, anote o `id` + assertion que falhou e ajuste a skill antes de commitar a mudança na skill.

 Rode os 3 evals após qualquer mudança em `abrir.md`, `comentar.md`, `pesquisar.md`, `regras-gerais.md`, `ferramentas.md` ou templates. Mudança só em `transferir.md`/`editar.md`/`ler.md` exige ao menos conferência manual do fluxo tocado.
