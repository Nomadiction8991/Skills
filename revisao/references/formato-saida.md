# Formato de Saída — Revisão Simples

Objetivo: relatório curto e direto, sem jargão. Baseado no exemplo aprovado pelo usuário.

## Cabeçalho

Primeira linha conforme o modo (ver `processo.md:1`):

- modo commit/local: `Revisão das alterações não commitadas (N arquivos). X achados, <resumo>.`
- modo mr/branch/ponto fixo: `Revisão da branch <atual> vs <base> (N arquivos, M commits). X achados, <resumo>.` ou `Revisão desde <ponto> (N arquivos, M commits). X achados, <resumo>.`

Exemplos:
- `Revisão das alterações não commitadas (3 arquivos). Três achados, nenhum bloqueia o que foi corrigido.`
- `Revisão da branch feature/repeat-order vs main (5 arquivos, 3 commits). 1 achado importante — precisa ajustar antes de mergear.`
- `Revisão das alterações não commitadas (2 arquivos). Nenhum achado — pronto para mergear.`

## Achados (se houver) — só o que o diff tocou

Lista numerada **sempre** do mais crítico ao menor. **Todo achado dentro do escopo do diff, mesmo pequeno, entra numerado aqui** — nunca esconder em parágrafo. Máximo 5; se houver mais, mantenha os 5 mais relevantes numerados e agrupe o resto como 1 item menor extra.

```
1. <Título curto> — <severidade>

<1-2 frases em linguagem comum explicando o problema e por que importa>

Onde: arquivo:linha — <nota curta, ex: comparar com :79 que já faz certo>
Sugestão: <ação concreta e verificável>
```

Severidade em texto simples (sem emoji, sem Crítico/Alto):
- `bloqueia` — quebra build/teste/funcionalidade principal, não mergear
- `importante` — comportamento errado, corrigir antes de mergear
- `menor` — risco baixo ou dívida técnica, pode ir no mesmo MR (mesmo menor dentro do escopo deve ser numerado aqui)

Exemplo real (1 achado dentro do escopo):

```
1. Taxa de entrega diferente entre a listagem e o detalhe do mesmo pedido — importante
No detalhe, a taxa mostrada é a que foi cobrada quando o pedido foi feito (valor gravado no pedido). Na listagem, a taxa é substituída pelo preço atual do bairro, quando esse bairro está ativo e tem valor. Se a loja reajustar a taxa do bairro depois, o cliente vê R$ 12 na lista e R$ 8 ao abrir o mesmo pedido — sem erro nenhum aparecendo.
Onde: app/Http/Controllers/Customer/RecentOrdersController.php:59 (listagem) vs resources/views/front/loja/clientes/pedido.blade.php:204 (detalhe). O total do detalhe (Order::total(), linha 125 do model) usa a taxa gravada, então é a listagem que está fora do padrão.
Sugestão: usar em ambas a taxa gravada no pedido, que é o valor realmente cobrado, e tirar o $bairro->valor do cálculo de resumoPedido().
```

## Eixos verificados sem achado

Um parágrafo único após os achados (ou após o cabeçalho se 0 achados). Só liste aqui eixos que **realmente não tiveram nenhum achado** — se um eixo tem achado fora do escopo, ele não entra aqui, vai na seção separada abaixo.

`Eixos verificados sem achado: <item>; <item>; <item>.`

Exemplo (quando 1 achado dentro do escopo e o resto ok):
`Eixos verificados sem achado: autorização (dono do pedido e loja conferidos via storeDoPedido(), com teste de 403 para cada caso, e firstOrFail() transformando slug inválido em 404); taxa de entrega (listagem e detalhe usam o mesmo campo gravado no pedido, com teste que reprova se voltar a recalcular pelo bairro); bairro (mesmos filtros de loja e status, mesmo fallback, dois testes); código morto (formatarTempo e $bairrosEntrega removidos sem referência — busca global; sem console.log/dd()); comentários (os dois adicionados explicam regra de negócio); timers (intervalo criado antes da primeira execução, cancelado antes de trocar para atraso, guarda contra duplicação e referência reatribuída após recriar HTML); consultas (nenhuma nova dentro do laço; uma a menos por acesso); testes (18 no arquivo, 1182 na suíte, nenhum afrouxado).`

Se não houver nenhum eixo sem achado (todos tiveram achado dentro do escopo), omita esta seção.

## Pontos fora do escopo (pré-existentes) — seção separada no final

Se houver achados **fora do escopo do diff** (pré-existentes que o diff não tocou), mostre-os **como achados numerados também, mas em seção separada no final**, depois do veredicto e antes da pergunta final — não misture com os achados do diff e não esconda em parágrafo solto.

Formato:

```
Pontos fora do escopo (pré-existentes, não introduzidos por este diff):

1. <Título> — menor (pré-existente)
Onde: arquivo:linha
Sugestão: ...

2. <Título> — menor (pré-existente)
Onde: arquivo:linha
Sugestão: ...
```

Exemplo real:
```
Pontos fora do escopo (pré-existentes, continuam fora deste diff):

1. Import não usado — menor (pré-existente)
Onde: app/Http/Controllers/Customer/RecentOrdersController.php:10
Sugestão: remover o use App\Models\AdditionalItems.

2. Comentário morto — menor (pré-existente)
Onde: app/Http/Controllers/Customer/RecentOrdersController.php:130
Sugestão: remover o comentário.

Também segue pendente o modal "Informações da loja", que lista bairros desativados — fora do escopo deste diff.
```

Se não houver fora do escopo, omita esta seção.

## Veredicto

Uma linha direta após os eixos (e antes dos pontos fora do escopo, se houver):

`Veredicto: <conclusão>.`

Exemplos:
- `Veredicto: as correções deste diff estão certas e podem ir para o MR. O ponto 1 vale corrigir no mesmo MR, porque nasceu no redesenho dessas duas telas.`
- `Veredicto: pronto para mergear.`

## Pergunta final

Se houve achados, termine com:

`Quer que eu aplique os N? E não há MR aberto para essas linhas ainda — se quiser que eu poste esta revisão como comentário, me diga o número.`

Se 0 achados, omita a pergunta.

## Regras

- Não use seções separadas `## Padrões` / `## Especificação` / `## Roda Reinventada` — unifique em uma lista numerada do mais crítico ao menor para achados dentro do escopo.
- **Todo achado dentro do escopo entra numerado em Achados**, mesmo `menor` — nunca em parágrafo solto. **Achados fora do escopo (pré-existentes) entram numerados também, mas na seção separada "Pontos fora do escopo" no final**, não na lista principal e não em "Eixos verificados sem achado".
- Não use blocos `Linguagem simples / Linguagem técnica / Recomendação` — use o formato curto acima (parágrafo + Onde + Sugestão).
- Sem emojis, sem tabelas de nível. Severidade é só a palavra no título.
- Se esta revisão corrige afirmações imprecisas de relatórios anteriores, acrescente antes do Veredicto: `Correção de relatório anterior: <o que foi corrigido>.`
