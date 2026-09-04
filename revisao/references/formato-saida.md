# Formato de Saída — Revisão Simples

Objetivo: relatório curto e direto, sem jargão. Baseado no exemplo aprovado pelo usuário.

## Linguagem dos achados

Escreva como se estivesse explicando o problema a uma pessoa que não conhece o código. Não mostre nomes de funções, variáveis, classes, arquivos, pastas, caminhos, linhas ou outros detalhes técnicos. Explique o que acontece, em que situação acontece, por que isso é um problema e como deve ser corrigido, usando termos do negócio e frases simples.

## Cabeçalho

Primeira linha conforme o modo (ver `processo.md:1`):

- modo git/local: `Revisão das alterações não commitadas (N arquivos). X achados, <resumo>.`
- modo branch/ponto fixo: `Revisão da branch <atual> vs <base> (N arquivos, M commits). X achados, <resumo>.` ou `Revisão desde <ponto> (N arquivos, M commits). X achados, <resumo>.`

Exemplos:
- `Revisão das alterações não commitadas (3 arquivos). Três achados, nenhum bloqueia o merge.`
- `Revisão da branch feature/repeat-order vs main (5 arquivos, 3 commits). 1 achado importante — precisa ajustar antes de mergear.`
- `Revisão das alterações não commitadas (2 arquivos). Nenhum achado — pronto para mergear.`

## Achados (se houver) — só o que o diff tocou

Lista numerada **sempre** do mais crítico ao menor. **Todo achado dentro do escopo do diff, mesmo pequeno, entra numerado aqui** — nunca esconder em parágrafo. Máximo 5; se houver mais, mantenha os 5 mais relevantes numerados e agrupe o resto como 1 item menor extra.

```
1. <Título curto> — <severidade>

<2-4 frases em linguagem simples explicando o que acontece, em que situação, por que isso é um problema e qual deve ser o resultado correto>

Como corrigir: <ação concreta descrita em linguagem de negócio, sem nomes ou caminhos do código — apenas orientação conceitual para o usuário, não aplicar>
```

Severidade em texto simples (sem emoji, sem Crítico/Alto):
- `bloqueia` — quebra build/teste/funcionalidade principal, não mergear
- `importante` — comportamento errado, corrigir antes de mergear
- `menor` — risco baixo ou dívida técnica, pode ir no mesmo MR (mesmo menor dentro do escopo deve ser numerado aqui)

Exemplo real (1 achado dentro do escopo):

```
1. O mesmo pedido mostra valores diferentes — importante
Ao consultar os pedidos, a pessoa vê um valor na lista e outro quando abre os detalhes do mesmo pedido. Isso pode acontecer depois que o preço de uma região é alterado, fazendo o histórico parecer errado e gerando dúvida sobre o que foi realmente cobrado.
Como corrigir: mostrar em todos os lugares o valor que foi cobrado no momento da compra.
```

## Veredicto

Uma linha direta após os achados:

`Veredicto: <conclusão>.`

Exemplos:
- `Veredicto: as correções deste diff estão certas e podem ir para o MR. O ponto 1 vale corrigir no mesmo MR, porque nasceu no redesenho dessas duas telas.`
- `Veredicto: pronto para mergear.`

## Resumo final

Sempre mostre um resumo ao final da revisão, depois do veredicto e antes da pergunta final. O resumo deve mencionar o escopo analisado, a quantidade de achados e a conclusão, sem incluir problemas fora do diff.

`Resumo final: escopo <git/local, branch <atual> vs <base> ou ponto <ponto>>; <X> achados; <conclusão em uma frase>.`

> **Importante:** O resumo final NUNCA deve trazer o código como "já corrigido" ou omitir os problemas encontrados. Ele deve sempre retratar fielmente os achados reais levantados para que o usuário decida posteriormente se vai corrigi-los.

## Pergunta final

Se houve achados, termine com:

`Deseja que eu ajude a corrigir algum desses pontos ou prefere manter como está? Se houver MR aberto e quiser que eu poste esta revisão como comentário, me diga o número.`

Se 0 achados, omita a pergunta.

## Regras

- **Apenas diagnóstico:** NUNCA aplique correções, NUNCA altere arquivos e NUNCA apresente o resumo final como se os problemas já estivessem resolvidos. Sempre mostre os problemas encontrados e pare para aguardar a decisão do usuário.
- Não use seções separadas `## Padrões` / `## Especificação` / `## Roda Reinventada` — unifique em uma lista numerada do mais crítico ao menor para achados dentro do escopo.
- **Todo achado dentro do escopo entra numerado em Achados**, mesmo `menor` — nunca em parágrafo solto. Problemas fora do escopo não entram no relatório.
- Não use blocos `Linguagem simples / Linguagem técnica / Recomendação` — use o formato curto acima (explicação + Como corrigir).
- Sem emojis, sem tabelas de nível. Severidade é só a palavra no título.
- Se esta revisão corrige afirmações imprecisas de relatórios anteriores, acrescente antes do Veredicto: `Correção de relatório anterior: <o que foi corrigido>.`
