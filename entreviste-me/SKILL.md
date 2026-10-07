---
name: entreviste-me
argument-hint: "[decisão]"
model: sonnet
effort: medium
description: "Use quando uma decisão relevante do usuário estiver realmente indefinida e afetar o resultado. Entreviste para resolver ambiguidades que não possam ser esclarecidas investigando o projeto; não bloqueie mudanças claras, pequenas ou reversíveis com perguntas de confirmação. Do NOT use for clear, small, or reversible changes — proceed directly."
---

Entreviste o usuário apenas sobre decisões relevantes que continuem sem resposta após investigar o contexto disponível. Não transforme toda mudança, ajuste ou plano em entrevista: se o objetivo estiver claro, execute; se um fato puder ser descoberto no projeto, descubra sem perguntar. Para decisões relevantes ainda abertas, modele o problema como uma **árvore de decisão**: cada decisão gera os ramos e dependências que dela decorrem.

Conduza a entrevista em **rodadas**. A **fronteira** é o conjunto de decisões cujos pré-requisitos já foram resolvidos — as perguntas que podem ser feitas agora sem presumir respostas ainda não dadas. Faça todas as perguntas da fronteira que sejam independentes entre si na mesma rodada, numerando-as e fornecendo uma resposta recomendada para cada uma. Aguarde minhas respostas antes de avançar para a próxima rodada.

Quando a entrevista for necessária, faça as perguntas da rodada **com a ferramenta de pergunta da sessão**, nunca como texto corrido: 1 a 4 perguntas por call, 2 a 4 opções por pergunta, cada uma com `label` + `description`. Marque a opção recomendada com "(Recomendada)" no fim do label e coloque-a em primeiro; a resposta customizada (usuário digitar a própria) já vem inclusa automaticamente. Não está disponível em subagents: se estiver em um, faça as perguntas a partir da thread principal.

Se a ferramenta não estiver disponível (ex.: execução não interativa), pergunte em texto com o mesmo formato — 2 a 4 opções numeradas, descrição curta do que cada uma implica, recomendada marcada e resposta própria permitida:

**P1 — <título da pergunta>**: <pergunta, incluindo contexto>

1. **<opção A>** — <implicação/descrição curta>
2. **<opção B>** — <implicação/descrição curta>
3. **<opção C>** — <implicação/descrição curta>

**Recomendada: 1**

Responda com o número da opção escolhida ou escreva a sua própria resposta.

Cada resposta deve remodelar a árvore: recalcule a fronteira e desbloqueie as decisões que dependem do que foi resolvido. Se uma pergunta depender de outra ainda aberta na rodada atual, deixe-a para uma rodada posterior.

Se um *fato* puder ser descoberto explorando o ambiente (arquivos, ferramentas, código), descubra você mesmo — não me pergunte. Quando uma pergunta da fronteira exigir essa descoberta, use as ferramentas ou um subagente e trate a investigação como pré-requisito pendente; faça imediatamente as demais perguntas independentes. Pergunte sobre decisões que realmente mudem o escopo, o comportamento esperado ou um efeito difícil de reverter. Para escolhas pequenas, locais e reversíveis com uma opção claramente alinhada ao pedido, adote essa opção e informe o que fez; não peça confirmação apenas para narrar uma suposição óbvia. As decisões relevantes que permanecerem abertas são do usuário — pergunte e aguarde.

## Linguagem

A skill `linguagem` está sempre disponível, não precisa checar "se existe". Acione-a por nome com `modo=simples-com-termos` para formatar todas as mensagens desta entrevista.

Se não conseguir acioná-la, siga o estilo padrão da resposta.

## Finalização

A entrevista termina quando as decisões relevantes estiverem resolvidas. Resuma brevemente o encaminhamento quando isso ajudar, mas não peça uma confirmação final adicional se o usuário já autorizou a tarefa e não restou decisão pendente; prossiga com a ação solicitada.

## Exemplo

Uma decisão relevante ainda indefinida pode mudar o resultado → faça 1–2 perguntas para esclarecer as opções.

## Troubleshooting

- Dá para resolver investigando o projeto → investigue; não entreviste.
- O usuário respondeu parcialmente → siga com o que tem.
