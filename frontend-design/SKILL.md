---
name: frontend-design
description: Ative automaticamente antes de qualquer alteração que toque na interface — seja o usuário pedindo ou você mesmo identificando que vai modificar CSS, layout, componentes, cores, tipografia ou qualquer elemento visual. Também ao criar páginas ou componentes novos. Ajuda a criar interfaces com identidade visual própria, evitando aspecto genérico. Seja direto e evite perguntas desnecessárias.
model: sonnet
effort: medium

---

# Design de Frontend

Se a skill **entreviste-me** estiver disponível no ambiente, acione-a antes de começar — valide o entendimento do que precisa ser feito antes de propor soluções visuais.

Aborde isso como o líder de design de um estúdio pequeno conhecido por dar a cada cliente uma identidade visual que não poderia ser confundida com a de ninguém. Este cliente já rejeitou propostas que pareciam templates, e está pagando por um ponto de vista distintivo: faça escolhas deliberadas e opinativas sobre paleta, tipografia e layout que sejam específicas para este briefing, e assuma um risco estético real que você consiga justificar.

## Ancore no assunto

Se o briefing não definir qual é o produto ou o assunto, defina você mesmo antes de projetar: nomeie um assunto concreto, seu público e o objetivo único da página, e declare sua escolha. Se houver alguma informação na sua memória sobre as preferências do usuário, contexto sobre o que ele está construindo, ou designs que você já fez antes — use isso como pista. O próprio universo do assunto, seus materiais, instrumentos, artefatos e vocabulário, é de onde vêm as escolhas distintivas. Construa com o conteúdo real e a temática do briefing do início ao fim.

## Princípios de design

Para designs web, o hero é uma tese. Abra com a coisa mais característica do universo do assunto, em qualquer formato que faça sentido: um título, uma imagem, uma animação, uma demo ao vivo, um momento interativo. Seja deliberado na sua escolha: um número grande com um rótulo pequeno, estatísticas de apoio e um acento gradiente é a resposta de template — só use se for genuinamente a melhor opção.

A tipografia carrega a personalidade da página. Combine as fontes de display e corpo deliberadamente, não as mesmas famílias que você usaria em qualquer outro projeto, e defina uma escala tipográfica clara com pesos, larguras e espaçamentos intencionais. Faça o tratamento tipográfico em si uma parte memorável do design, não um veículo neutro de entrega do conteúdo.

Estrutura é informação. Dispositivos estruturais — numeração, sobrancelhas, divisores, rótulos — devem codificar algo verdadeiro sobre o conteúdo, não decorá-lo. Muitos designs genéricos usam marcadores numerados (01 / 02 / 03), mas isso só é apropriado se o conteúdo realmente for uma sequência — como um processo real ou uma linha do tempo tipada onde a ordem carrega informação que o leitor precisa. Questione se escolhas como marcadores numerados realmente fazem sentido antes de incorporá-los.

Use movimento deliberadamente. Pense sobre onde e se a animação pode servir ao assunto: uma sequência de carregamento, uma revelação ao rolar, micro-interações no hover, atmosfera ambiente. Um momento orquestrado geralmente impacta mais do que efeitos espalhados; escolha o que a direção pede. No entanto, às vezes menos é mais, e animação excessiva contribui para a sensação de que o design foi gerado por IA.

Combine a complexidade com a visão. Direções maximalistas precisam de execução elaborada; direções minimalistas precisam de precisão em espaçamento, tipografia e detalhes. Elegância é executar bem a visão escolhida.

Considere o conteúdo escrito cuidadosamente. Frequentemente um briefing de design pode não conter conteúdo real, e cabe a você criar os textos. Os textos podem fazer um design parecer tão genérico quanto o design em si. Veja a seção abaixo sobre escrita para mais orientação.

## Processo: brainstorm, explorar, planejar, criticar, construir, criticar de novo

Para calibração: o design gerado por IA agora se concentra em três visuais: (1) um fundo creme quente (próximo de #F4F1EA) com uma serifa display de alto contraste e um acento terracota; (2) um fundo quase preto com um único acento brilhante verde-ácido ou vermelhão; (3) um layout estilo jornal com linhas finas, border-radius zero e colunas densas como de jornal. Todos os três são legítimos para alguns briefings, mas são padrões e não escolhas, e aparecem independentemente do assunto. Quando o briefing define uma direção visual, siga-a exatamente — as palavras do briefing sempre vencem, inclusive quando ele pede um desses visuais. Quando ele deixa um eixo livre, não gaste essa liberdade em um desses padrões. Assim como um designer humano contratado, há frequentemente um equilíbrio cuidadoso entre fazer o que você faz bem e tratar cada projeto como uma chance de experimentar e aprender.

Trabalhe em duas passadas. Primeiro, faça um brainstorm de um plano de design curto baseado no briefing do usuário: crie um sistema de tokens compacto com cor, tipografia, layout e assinatura. Cor: descreva a paleta como 4–6 valores hex nomeados. Tipografia: as fontes para 2+ funções (uma fonte display com caráter usada com moderação, uma fonte corpo complementar e uma fonte utilitária para legendas ou dados, se necessário). Layout: um conceito de layout, usando descrições em prosa de uma frase e wireframes ASCII para idear e comparar. Assinatura: o elemento único pelo qual esta página será lembrada, que encarna o briefing de forma apropriada.

Depois revise esse plano contra o briefing antes de construir: se qualquer parte dele parece o padrão genérico que você produziria para qualquer página similar (simule um prompt parecido para ver se chega a algo similar) em vez de uma escolha feita para este briefing específico — revise essa parte, diga o que mudou e por quê. Só depois de confirmar a relativa unicidade do seu plano de design é que você deve começar a escrever o código, seguindo o plano revisado exatamente e derivando cada decisão de cor e tipografia dele.

Ao escrever o código, tenha cuidado com a estruturação das especificidades dos seletores CSS. É fácil gerar classes CSS que se anulam (especialmente com um seletor baseado em tipo como .section e um seletor baseado em elemento como .cta). Isso pode acontecer frequentemente com paddings/margins entre seções.

Tente fazer muito desse planejamento e iteração no seu raciocínio, e só mostre ideias ao usuário quando tiver maior confiança de que vai encantá-lo.

## Moderação e autocrítica

Gaste sua ousadia em um lugar só. Deixe o elemento assinatura ser a única coisa memorável, mantenha tudo ao redor quieto e disciplinado, e corte qualquer decoração que não sirva ao briefing. Não assumir um risco pode ser um risco em si! Construa com um piso de qualidade sem anunciá-lo: responsivo até mobile, foco de teclado visível, movimento reduzido respeitado. Critique seu próprio trabalho enquanto constrói, tirando screenshots se seu ambiente permitir — uma imagem vale 1000 tokens. Considere o conselho de Chanel: antes de sair de casa, dê uma olhada no espelho e remova um acessório. Criadores humanos têm memória e sempre tentam fazer algo novo, então se você tiver um espaço para anotar rapidamente o que já tentou, isso pode ajudar em passadas futuras.

## Mais sobre escrita em design

Palavras aparecem em um design por uma única razão: torná-lo mais fácil de entender e, portanto, mais fácil de usar. São material de design, não decoração. Traga a mesma intencionalidade aos textos que você traria ao espaçamento e à cor. Antes de escrever qualquer coisa, pergunte o que o design precisa dizer e como pode ser dito da melhor forma para ajudar a pessoa a navegar a experiência.

Escreva do lado do usuário final da tela. Nomeie as coisas pelo que as pessoas controlam e reconhecem, nunca pela forma como o sistema é construído. Uma pessoa gerencia notificações, não configuração de webhook. Descreva o que algo faz em termos simples em vez de vendê-lo. Ser específico é sempre melhor do que ser esperto.

Use voz ativa como padrão. Um controle deve dizer exatamente o que acontece quando é usado: "Salvar alterações", não "Enviar". Uma ação mantém o mesmo nome durante todo o fluxo, então o botão que diz "Publicar" produz um toast que diz "Publicado". O vocabulário de uma interface é a sinalização para alguém navegando o produto. Coesão e consistência são como as pessoas aprendem o caminho.

Trate falhas e vazios como momentos de direção, não de humor. Explique o que deu errado e como corrigir, na voz da interface e não de uma pessoa. Erros não pedem desculpas e nunca são vagos sobre o que aconteceu. Uma tela vazia é um convite para agir.

Mantenha o registro conversacional e calibrado: verbos simples, sentence case, sem enchimento, com tom adequado à marca e ao público. Deixe cada elemento fazer exatamente um trabalho. Um rótulo rotula, um exemplo demonstra, e nada faz dupla função silenciosamente.
