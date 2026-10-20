# Formato de Saída — Revisão Simples

Objetivo: relatório curto e direto, sem jargão. Baseado no exemplo aprovado pelo usuário.

## Linguagem dos achados

Escreva como se estivesse explicando o problema a uma pessoa que não conhece o código. A explicação usa termos do negócio e frases simples: o que acontece, em que situação, por que é um problema e qual deve ser o resultado correto. Nomes de função/variável ficam fora da explicação.

Cada achado termina com uma linha `Onde:` com âncora curta para achar o ponto em 30 segundos (arquivo, tela/ação ou fluxo — ex.: `Onde: tela de cartas / ordenação por coluna`). Sem SHA e sem número de linha no relatório final (linha fica só na validação interna).

## Cabeçalho

Primeira linha conforme o modo (formatos abaixo; cabeçalho de cada modo em `git.md`, `branch.md` ou `processo.md` Parte B):

- modo git/local: `Revisão das alterações não commitadas (N arquivos). X achados, <resumo>.`
- modo branch/ponto fixo: `Revisão da branch <atual> vs <base> (N arquivos, M commits). X achados, <resumo>.` ou `Revisão desde <ponto> (N arquivos, M commits). X achados, <resumo>.`

Exemplos:
- `Revisão das alterações não commitadas (3 arquivos). Três achados, nenhum bloqueia o merge.`
- `Revisão da branch feature/repeat-order vs main (5 arquivos, 3 commits). 1 achado importante — precisa ajustar antes de mergear.`
- `Revisão das alterações não commitadas (2 arquivos). Nenhum achado — pronto para mergear.`

## Achados (se houver) — só o que o diff tocou

Lista numerada **sempre** do mais crítico ao menor. **Todo achado validado entra numerado aqui** — nunca esconder em parágrafo, nunca cortar por limite numérico, nunca agrupar à força: o contexto já foi lido e validado, cada achado real vale o espaço.

```
1. <Título curto> — <severidade>

<2-4 frases em linguagem simples explicando o que acontece, em que situação, por que isso é um problema e qual deve ser o resultado correto>

Como corrigir: <ação concreta em linguagem de negócio — apenas orientação conceitual, não aplicar>
Onde: <arquivo, tela/ação ou fluxo — âncora curta, sem SHA/linha>
```

Severidade em texto simples (sem emoji, sem Crítico/Alto):
- `bloqueia` — quebra build/teste/funcionalidade principal, não mergear
- `importante` — comportamento errado, corrigir antes de mergear
- `menor` — risco baixo ou dívida técnica, pode ir no mesmo MR

Exemplo real (1 achado dentro do escopo):

```
1. O mesmo pedido mostra valores diferentes — importante
Ao consultar os pedidos, a pessoa vê um valor na lista e outro quando abre os detalhes do mesmo pedido. Isso pode acontecer depois que o preço de uma região é alterado, fazendo o histórico parecer errado e gerando dúvida sobre o que foi realmente cobrado.
Como corrigir: mostrar em todos os lugares o valor que foi cobrado no momento da compra.
Onde: tela de pedidos / lista vs detalhes
```

Exemplo com 2 achados (ordem: bloqueia primeiro, `Onde:` em todo item):

```
1. Falha de rede apaga um registro que pode ter sido efetivado — bloqueia
Se a resposta do serviço externo se perde no caminho, o fluxo trata como falha definitiva e apaga o registro local. Quando o serviço já tinha efetivado, o dado some dos dois lados e a nova tentativa é rejeitada como duplicidade.
Como corrigir: tratar falha de transporte como estado indeterminado, sem apagar, com caminho próprio de conciliação.
Onde: envio ao serviço externo / tratamento de timeout

2. Comentário em idioma diferente da convenção — menor
O projeto comenta em português e o diff trouxe um comentário em inglês. Não quebra nada, mas destoa do padrão que o time lê.
Como corrigir: reescrever o comentário no idioma da convenção do arquivo.
Onde: migration nova / comentário no topo
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
- **Todo achado validado entra numerado em Achados** — nunca em parágrafo solto, nunca cortado por limite. Problemas fora do escopo não entram no relatório.
- Não use blocos `Linguagem simples / Linguagem técnica / Recomendação` — use o formato curto acima (explicação + Como corrigir + Onde).
- Sem emojis, sem tabelas de nível. Severidade é só a palavra no título.
- Se esta revisão corrige afirmações imprecisas de relatórios anteriores, acrescente antes do Veredicto: `Correção de relatório anterior: <o que foi corrigido>.`
