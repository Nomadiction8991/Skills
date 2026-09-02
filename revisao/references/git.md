# Modo Git / Local — /revisao e /revisao git

Revisa alterações não commitadas. Sempre chama agents com contexto limpo. Captura do diff conforme `processo.md#1`.

## 1. Ler arquivos completos

Abrir cada arquivo tocado pelo diff por inteiro. O diff esconde método irmão que já valida e o lugar que já calcula o mesmo dado.

## 2. Lançar agents (contexto limpo)

Disparar em paralelo com `general-purpose`, cada um com o diff + lista de commits + arquivos completos relevantes:

**Agent A — Autorização, divergência e borda:**
Briefing: "Verifique 1) autorização/escopo do dono (autorização e tenant), 2) mesma informação derivada de formas divergentes, 3) borda dos dados. Use `references/validacoes.md#1,2,6`. Explique cada achado em linguagem de negócio, sem nomes de código ou caminhos técnicos. Até 300 palavras, linguagem simples."

**Agent B — Código morto, padrões, infra e testes:**
Briefing: "Verifique 1) código morto/indireção/duplicação, 2) responsabilidade no lugar certo, 3) consistência com vizinhos, 4) padrões do stack, 5) infra prod enxuta, 6) pipeline/config, 7) migração, 8) idioma consistente, 9) rodar todos os testes (identifique via Makefile/README/package.json e rode). Use `references/validacoes.md#3,4,5,6,8,9,10,11,12,13,14` + `code-smells.md`. Até 350 palavras, linguagem simples."

Cada agent já recebe `validacoes.md` colada no prompt — não precisa ler de novo.

## 3. Validar antes de agregar

Para cada achado dos agents: confirmar internamente no código atual, construir cenário concreto ("uma pessoa abre um pedido e vê dados de outra"), descartar se não descrever, se estiver fora do diff ou se for pré-existente, não inflar severidade. Reescrever o resultado sem nomes de código ou caminhos técnicos.

## 4. Agregar

Seguir `formato-saida.md` e `processo.md#5`. Cabeçalho: `Revisão das alterações não commitadas (N arquivos)`. Sempre incluir o `Resumo final` antes da pergunta final.
