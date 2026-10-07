# Referência — Pesquisar / Resumir Chamados

Fluxo para quando o usuário **não** informa um número de chamado específico e pede uma pesquisa,
um resumo ou uma visão geral (ex.: "resumo dos chamados do cliente X", "quantos chamados abertos
no Suporte", "o que tem pra Ello Tecnologia esse mês", "lista os chamados urgentes").

Fluxo **somente leitura** — não precisa de confirmação do usuário antes de executar (regra 1 de
`regras-gerais.md` só se aplica a escrita: criar/editar/comentar/transferir).

---

## O que a API do TomTicket (via MCP) realmente suporta filtrar

Só existem **dois filtros nativos** em `listar_chamados`, e são **mutuamente exclusivos** (a tool
não combina os dois na mesma chamada):

| Parâmetro | Tipo | Observação |
|---|---|---|
| `status` | string | Default `abertoequipe`. Outros valores conhecidos: `finalizadoequipe`, `canceladoequipe`, `fila`. Também aceita nome de status personalizado (ver `listar_status`) — testar se necessário. |
| `departamento` | string | Nome do departamento (ver `listar_departamentos`). Quando informado, a tool **ignora `status`** e retorna os chamados em aberto daquele departamento. **Confirmado empiricamente:** chamar com `departamento="Suporte"` + `status="finalizadoequipe"` juntos devolveu exatamente o mesmo resultado que só `departamento="Suporte"` (os 33 abertos, não os finalizados) — `status` é silenciosamente descartado quando `departamento` está presente, não há erro nem aviso. Não existe forma de pedir "finalizados de um departamento específico" nessa tool. |
| `pagina` | int | Paginação — **só funciona quando o filtro é `status`**. Confirmado empiricamente: 30 chamados por página em `abertoequipe` e `finalizadoequipe` (páginas 1 e 2 vieram cada uma com 30 números diferentes, sem sobreposição). **Por `departamento`, `pagina` é ignorado**: testado com "Suporte" e "Desenvolvimento", página 1 e página 2 retornaram exatamente os mesmos chamados (33 em cada, incluindo tickets desde 2023/2025) — ou seja, filtrar por departamento devolve **todo o histórico em aberto daquele departamento de uma vez**, sem paginar. Nada disso está no schema da tool (`pagina` é só um `int` sem limite declarado) — é observação empírica, não garantia documentada da API. |

**Não existem parâmetros nativos para:** cliente, data/período, categoria, atendente/operador,
prioridade, nem para combinar dois filtros ao mesmo tempo (ex.: "departamento X **e** cliente Y").

## Como filtrar por essas outras dimensões na prática

Cada card retornado por `listar_chamados` já traz como texto: `Cliente`, `Dept`, `Atendente`,
`Prioridade`, `Data`. Como a API não filtra por eles, o filtro é feito **client-side**, depois de
buscar os dados brutos:

1. Chamar `listar_chamados` com o filtro nativo mais próximo do pedido (`status` ou `departamento`).
   Se o pedido não bate com nenhum dos dois (ex.: "chamados do cliente X" sem falar de status/depto),
   usar o default (`status=abertoequipe`) como ponto de partida — e avisar ao usuário que a busca
   cobre chamados em aberto, não o histórico completo, a menos que ele peça encerrados/cancelados.
2. Paginar (`pagina=2, 3, ...`) até esgotar os resultados relevantes ao período/quantidade pedida
   pelo usuário (a resposta não indica "tem próxima página" de forma explícita — parar quando uma
   página vier vazia ou repetir números já vistos).
3. Filtrar em memória pelo texto de cada card: nome do cliente (substring, case-insensitive),
   janela de datas (`Data: dd/mm/aaaa`), prioridade, atendente etc.
4. Se o usuário quer **combinar** filtros (ex.: cliente + departamento), buscar pelo filtro nativo
   que reduz mais o volume (departamento, se informado) e aplicar o resto client-side. **Exceção:**
   "status X de um departamento específico" (ex.: finalizados só do Suporte) não dá pra pedir direto
   — `departamento` sempre ignora `status` (só devolve os abertos daquele departamento). Nesse caso,
   buscar por `status` (paginando) e filtrar o campo `Dept` do card client-side, não usar `departamento`.

### Limitação a comunicar ao usuário

Se o pedido implica um universo muito grande (ex.: "todos os chamados do cliente X desde sempre",
sem recorte de status/departamento), avisar que a varredura é limitada às páginas percorridas — não
existe uma busca server-side por cliente ou data, então cobrir "tudo" pode exigir muitas chamadas.
Nesse caso, perguntar se o usuário aceita recortar por status (aberto/fechado) ou por um período
aproximado antes de continuar, em vez de paginar indefinidamente.

## Buscar cliente (`buscar_cliente`) — não serve para listar chamados do cliente

`mcp__tomticket__buscar_cliente(q)` retorna `cliente_id`, nome e e-mail — é usado para **criar**
chamados (`cliente_id` em `criar_chamado`), não para filtrar `listar_chamados` (que não aceita
`cliente_id`). Não tentar usar o resultado de `buscar_cliente` como filtro de listagem — o nome do
cliente já vem em texto em cada card de `listar_chamados`, e é ali que a comparação deve ser feita.

## Detalhe completo de um chamado específico (para resumos mais profundos)

`listar_chamados` só devolve um resumo por card (título, cliente, dept, atendente, prioridade,
data). Se o pedido exigir o conteúdo/histórico completo de cada chamado (não só a lista), chamar
`mcp__tomticket__ver_chamado(numero)` por chamado relevante — caro em chamadas se a lista for
grande, então só fazer isso para um subconjunto pequeno (ex.: os 3-5 chamados mais relevantes),
avisando o usuário se for preciso restringir por esse motivo.

## Categorias, operadores e status como filtros de contexto (não de busca)

- `listar_categorias(departamento)` e `listar_operadores(departamento)` e `listar_status(departamento)`
  servem para **decodificar/validar** valores (ex.: confirmar nome exato de status antes de usar em
  `status=`), não para filtrar `listar_chamados` diretamente — nenhum deles é aceito como parâmetro
  de `listar_chamados` além do que já foi listado.
- `listar_status(departamento=X)` **filtra de fato** a lista de status pelo departamento informado
  (confirmado: sem `departamento` vieram 11 status; com `departamento="Suporte"` vieram só 7 — os
  que não fazem sentido pro Suporte, como "Atendimento encaminhado para Desenvolvimento", saem da
  lista). Útil pra validar o nome exato de um status personalizado antes de tentar usar em `status=`
  de `listar_chamados` (embora não tenha sido confirmado que `listar_chamados` aceite status
  personalizado — só os 4 buckets padrão foram testados com sucesso, ver abaixo).

---

## Testes empíricos adicionais (rodada 2 — só leitura)

- **`status="fila"`** → `"Nenhum chamado encontrado."` (sem erro; hoje não há chamados na fila).
- **`status="canceladoequipe"`** → 9 chamados, todos numa página só (abaixo do corte de 30, não
  precisou paginar).
- **`departamento` é case-insensitive, mas exige o nome completo** — `departamento="suporte"`
  (minúsculo) funcionou igual a `"Suporte"`. Já `departamento="Dev"` (abreviado/parcial) **deu erro**:
  não faz correspondência parcial/fuzzy, só normaliza case.
- **Departamento inválido/desconhecido → erro explícito e útil**, não silêncio nem lista vazia:
  a mensagem de erro já lista todos os departamentos disponíveis (`Departamento 'X' não encontrado.
  Disponíveis: 1 - Suporte, 2 - Triagem, ...`). Dá pra aproveitar esse erro como fallback de
  validação em vez de sempre chamar `listar_departamentos` antes.
- **Página além do fim (`status="abertoequipe"`, `pagina=99`) → `"Nenhum chamado encontrado."`**,
  sem erro. Confirma que dá pra parar de paginar assim que uma página vier vazia — não precisa
  adivinhar o total antes.
- **`buscar_cliente(q)` casa substring em nome OU e-mail**, não só no nome: buscar `"Ello"` trouxe
  16 clientes, incluindo nomes que não contêm "Ello" no nome (ex.: "AGROFORCE COMERCIO...") só
  porque o e-mail é `@ellotecnologia.net.br`/`.com`. Cuidado ao usar isso pra "resumo de chamados do
  cliente Ello": o próprio `buscar_cliente` já pode trazer ruído (empresas terceiras cujo e-mail é
  do domínio Ello) — não equivale a "chamados abertos pela empresa Ello Tecnologia".
- **`ver_chamado(numero)` traz descrição completa (todas as seções do corpo) + histórico completo**,
  incluindo respostas do time. Bom para resumo profundo de 1 chamado específico, mas caro em volume
  (1 chamada de API por chamado) — não usar em massa, só no recorte final já filtrado.

---

## Fluxo

### Passo 1 — Entender o pedido

Identificar o que o usuário quer: cliente, departamento, status (aberto/fechado/cancelado/fila),
período, prioridade, atendente, ou combinação. Se ambíguo (ex.: só "resumo dos chamados"), perguntar
o recorte principal antes de buscar:
> "Resumo de quais chamados? Por cliente, departamento, status (aberto/fechado) ou período?"

**Também identificar aqui se o pedido já pede profundidade explicitamente** (ex.: "resumo
**detalhado**", "quero saber o que tem **dentro** de cada um", "resume o conteúdo dos chamados da
minha semana"). Isso muda o Passo 4/5 — ver nota lá embaixo. Um pedido genérico ("resumo dos
chamados da minha semana", "quantos chamados abertos") **não** pede profundidade por padrão — o
padrão é só listar (Passo 4) e **oferecer** aprofundar depois (Passo 5), nunca entrar em cada
chamado de cara sem essa deixa explícita.

### Passo 2 — Buscar os dados brutos

Chamar `listar_chamados` com o filtro nativo mais próximo (status ou departamento), paginando
conforme necessário (ver seção acima).

### Passo 3 — Filtrar e agregar

Aplicar os filtros adicionais client-side (cliente, data, prioridade, atendente) sobre o texto dos
cards retornados. Se o pedido for um resumo (não uma lista crua), agregar por categoria pedida —
contagem por status, por departamento, por prioridade, por cliente etc., conforme o que fizer
sentido para a pergunta.

### Passo 4 — Apresentar o resultado

Resumo direto: números agregados primeiro (ex.: "14 chamados abertos no Suporte, 5 de alta
prioridade"), lista dos itens relevantes em seguida (número, título, cliente, data) — sem
formatação HTML (isso é só para corpo de chamado, não para resposta ao usuário no chat).

Se a varredura foi parcial (por limite de páginas ou recorte de status), dizer isso explicitamente
no resumo, não deixar implícito que é o total absoluto.

### Passo 5 — Aprofundar em algum chamado

A lista do Passo 4 só tem o resumo raso de cada card (título, cliente, dept, atendente, prioridade,
data) — não o conteúdo real do chamado.

**Se o Passo 1 já identificou profundidade explícita no pedido** (ex.: "resumo **detalhado**",
"resume o **conteúdo** de cada um"), pular a pergunta abaixo: chamar `ver_chamado(numero)` já para
todos os chamados da lista (ou o recorte relevante, se a lista for grande — avisando o usuário que
restringiu por volume) e apresentar o resumo já aprofundado direto, sem esperar confirmação.

**Caso contrário (pedido genérico, é o caso mais comum)**, depois de mostrar a lista crua do Passo 4,
**sempre perguntar** se o usuário quer aprofundar em algum(ns) chamado(s) específico(s):

> "Quer que eu detalhe algum desses chamados? Pode me passar o(s) número(s), ou pedir 'os N mais
> [urgentes/recentes/relevantes]' que eu já busco os detalhes."

- Se o usuário passar número(s) específico(s), ou pedir um recorte (ex.: "os 3 mais urgentes"),
  chamar `mcp__tomticket__ver_chamado(numero)` para cada um (ver nota de custo acima — 1 chamada por
  chamado, então não fazer isso para a lista inteira de uma vez, só para o recorte pedido) e
  apresentar a descrição/histórico relevante de cada um.
- Se o usuário disser que não precisa, encerrar o fluxo por aqui.
- Não pular esta pergunta mesmo quando o pedido original já parecia ser só "uma lista" — o usuário
  pode não saber de antemão que dá pra aprofundar, então oferecer sempre é o comportamento padrão
  deste fluxo.
