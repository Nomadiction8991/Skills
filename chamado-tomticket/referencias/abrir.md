# Referência — Abrir Chamado

Passo a passo completo para abertura de chamados no TomTicket.

---

## Princípios do conteúdo

Ver `regras-gerais.md` (regra #3) — vale integralmente para o título e o corpo construídos neste fluxo.

Se o usuário anexou um arquivo nesta conversa (ou pedir para incluir um): imagem → ver `imagens.md`; outro tipo de arquivo → ver `anexos.md`. Em ambos os casos só é possível anexar/embutir depois que o chamado existir (via edição), nunca na criação em si.

---

## Pré-requisito

A descrição do problema deve ter pelo menos 30 caracteres. Se não tiver, interrompa e peça ao usuário que descreva melhor antes de continuar.

---

## Passo 1 — Coletar Informações

Faça no máximo uma pergunta por vez. Tente inferir antes de perguntar.

### a) Cliente *(sempre perguntar)*

> "Qual é o cliente? `[1] Ello Tecnologia (padrão)` `[2] Outro cliente`"

- **1:** usar **Ello Tecnologia**.
- **2:** perguntar o nome do cliente.

Com o nome definido, chamar `buscar_cliente(q="<nome>")` para obter o `cliente_id`. Se a busca não retornar resultado, informar ao usuário e pedir o nome correto.

### b) Departamento *(sempre perguntar)*

Chamar `listar_departamentos()` para obter os departamentos que o operador logado realmente tem acesso (pode ser um subconjunto da tabela de referência em `SKILL.md`).

Escolher o sugerido com base no contexto:

| Contexto | Departamento sugerido |
|---|---|
| Erro, bug, falha, lentidão | Suporte ou Triagem |
| Melhoria ou defeito em aplicação web (navegador) | Desenvolvimento/Web |
| Melhoria ou defeito em aplicação desktop (Windows) | Desenvolvimento |
| Implantação, onboarding, configuração inicial | IMPLANTAÇÃO |
| Representante ou parceiro comercial | UAR |
| Dúvida geral ou contexto pouco claro | Triagem |

Apresentar a lista retornada pela API, **sempre com o sugerido como `[1]`** e os demais em seguida na ordem retornada (`[2]`, `[3]`, ...) — a numeração não é fixa por departamento, é sempre "sugerido primeiro":

> "Qual é o departamento? `[1] <sugerido>` (recomendado) `[2] <próximo>` `[3] <próximo>` ..."

Aceitar tanto o número quanto o nome digitado pelo usuário.

### c) Categoria *(sempre perguntar)*

Chamar `listar_categorias(departamento="<escolhido>")` para obter as categorias que o departamento escolhido realmente tem disponíveis.

Apresentar a lista retornada pela API, **sempre com a mais adequada ao contexto como `[1]`** e as demais em seguida (`[2]`, `[3]`, ...):

> "Qual é a categoria? `[1] <sugerida>` (recomendada) `[2] <próxima>` `[3] <próxima>` ..."

Aceitar tanto o número quanto o nome digitado pelo usuário.

### d) Prioridade *(sempre perguntar)*

Prioridade é um enum fixo da API (`baixa | normal | alta | urgente`) — não há tool de listagem para consultar antes.

| Critério | Prioridade |
|---|---|
| Sistema fora do ar, perda de dados, impacto total | `urgente` |
| Funcionalidade principal indisponível, muitos usuários | `alta` |
| Funcionalidade parcialmente afetada, workaround existe | `normal` |
| Cosmético, dúvida, melhoria, impacto mínimo | `baixa` |

Estimar a sugestão pelo contexto e apresentar como `[1]`, com as demais em seguida na ordem de proximidade com a sugestão:

> "Qual é a prioridade? `[1] <sugerida>` (recomendada) `[2] <próxima>` `[3] <próxima>` `[4] <última>`"

Aceitar tanto o número quanto o nome digitado pelo usuário.

### e) Descrição detalhada

Usar o texto de `args` como base. Expandir com contexto coletado. Se vago ou menor que 50 caracteres, pedir mais detalhes.

### f) Passos para reproduzir *(opcional)*

Nem todo chamado precisa — só incluir a seção quando for possível descrever um passo a passo real:
- O usuário já explicou os passos na descrição, ou
- É possível inferir com confiança a partir do contexto/descrição.

Se não for nenhum dos dois casos, **não perguntar** e não inventar passos genéricos: no preview em Markdown mostrar "Não aplicável" (ver `../templates/chamado.md`), mas no HTML enviado à API **omitir a seção inteira** (`<h4>Passos para Reproduzir</h4>` + `<ol>`) — não escrever "Não aplicável" dentro do HTML.

### g) Comportamento esperado vs. observado

Derivar do contexto. Perguntar apenas se não for possível inferir os dois.

### h) Impacto

Estimar pelo contexto. Se não for possível, perguntar: "Quantos usuários ou processos são afetados?"

**Justificativa:** gerar uma sugestão a partir do contexto coletado (prioridade, impacto, comportamento observado) e apresentar para o usuário aprovar, complementar ou reescrever — nunca inserir a justificativa sem mostrar antes:

> "Justificativa sugerida: '<texto gerado>'. `[1] Usar como está` `[2] Adicionar mais detalhes` `[3] Reescrever`"

### i) Responsável *(sempre perguntar)*

> "Deseja atribuir um responsável? `[1] Eu mesmo` `[2] Outra pessoa` `[3] Não atribuir`"

- **1:** usar o operador logado como responsável.
- **2:** perguntar o nome da pessoa.
- **3:** não preencher `operador`.

Para **1** ou **2**, chamar `listar_operadores(departamento=<escolhido>)` e localizar o operador pelo nome (o logado, em **1**; o informado, em **2**). Se não encontrar, informar ao usuário e deixar em branco.

### j) Ambiente *(sempre perguntar)*

Sugerir **Produção** por padrão, salvo se o contexto indicar claramente outro ambiente (ex.: usuário menciona homologação/teste/desenvolvimento):

> "Qual é o ambiente afetado? `[1] <sugerido>` (recomendado) `[2] <próximo>` `[3] <próximo>` `[4] Não informar`"

---

## Passo 2 — Investigar o Codebase *(quando houver codebase disponível)*

Este passo só se aplica quando a skill está sendo usada dentro de um projeto/codebase real. Se não houver projeto aberto, ou o módulo/tela mencionado não for encontrado no código, **pular este passo silenciosamente** e seguir só com o que o usuário informou — não é erro, não precisa avisar o usuário.

Quando houver codebase, antes de construir o título e o corpo, explorar o código relacionado ao módulo/tela mencionado na descrição.

**Objetivo:** entender melhor o problema — em qual tela/fluxo acontece, o que dispara, o que o usuário vê. A investigação é só para *você* compreender e descrever o problema com precisão; **os detalhes técnicos descobertos NÃO vão para o corpo do chamado** (ver Princípios do conteúdo).

### Como investigar

1. **Identificar o módulo/tela** a partir da descrição do usuário (ex: "Configurações", "Pedidos", "Dados da Empresa").

2. **Localizar os arquivos relevantes** usando buscas no codebase:
   - Componentes/telas: procurar por nome da tela em arquivos `.vue`, `.tsx`, `.blade.php`, etc.
   - Controllers/rotas: identificar a rota ou controller associado à funcionalidade.
   - Models/migrations: verificar tabelas e campos envolvidos.

3. **Ler os arquivos encontrados** para entender:
   - Quais campos existem na tela
   - Como os dados são salvos (qual tabela/coluna)
   - Qual rota/método é chamado
   - Se há algum detalhe técnico relevante para o contexto do chamado

4. **Olhar o contexto ao redor, não só o diff/commit em questão.** Se a descrição vier de uma mudança de código (commit, PR, correção), não se limitar aos arquivos alterados nele. Verificar também:
   - Se a rota/tela afetada é de fato referenciada/acessível em algum lugar da UI (links, botões, menus) — uma falha em código morto ou nunca alcançado por um usuário real não é a mesma coisa que uma falha observada em uso.
   - Testes relacionados (antes/depois) que indiquem o comportamento real.
   - Se existe qualquer relato, log ou evidência de que o problema de fato ocorreu na prática, ou se é um achado só de leitura de código.

   Isso muda o conteúdo do chamado: só descrever como "observado" o que realmente aconteceu para um usuário; achados puramente técnicos sem manifestação real devem ser descritos como tal (ex.: "identificado em revisão de código, sem uso real da tela").

5. **Entender o problema** a partir do que encontrar:
   - Em qual tela/fluxo o problema acontece
   - O que o usuário faz que dispara o problema
   - O que deveria acontecer e o que acontece de errado, do ponto de vista do usuário

### O que fazer com o que encontrar

Use o que descobriu apenas para **descrever o problema em linguagem de negócio** — pela ótica de quem usa o sistema. Traduza qualquer achado técnico para o que o usuário percebe na tela:
- ❌ Não escrever: "`ProductController::show()` retorna 200 em vez de 302" / "coluna `cor_texto` ausente na tabela `companies`"
- ✅ Escrever: "Ao abrir os detalhes de um produto, aparece uma tela em branco com código no lugar da página" / "Não é possível salvar a cor do texto nas configurações da loja"

Nada de nomes de arquivo, rota, tabela, coluna, método ou status HTTP no corpo. Nunca incluir a solução/correção.

> Se após a busca não for encontrado nada relevante, continuar normalmente com o que o usuário forneceu.

---

## Passo 3 — Construir o Título

Gerado automaticamente — só perguntar ao usuário se não conseguir identificar o nome do sistema ou o módulo com confiança (nada encontrado, ou mais de uma opção plausível e ambígua).

**Formato:**
```
Nome do Sistema (Módulo) - Descrição concisa do chamado
```

**Proibido:**
- Nunca colocar o nome do sistema entre colchetes
- Nunca usar formato `[Nome do Sistema] ...`
- Se editar ou reaproveitar um título existente que já tenha `[Sistema]`, remover apenas os colchetes e manter o nome do sistema

**Para descobrir o nome do sistema**, ler os arquivos `.md` da raiz:
```bash
find . -maxdepth 1 -name "*.md" | sort | head -5
```

Se não encontrar nome de sistema (nenhum `.md` útil, sem codebase disponível) ou módulo (não identificável pela descrição/Passo 2) — ou tiver dúvida entre opções — perguntar diretamente ao usuário em vez de arriscar um nome errado.

**Exemplos:**
- `Ello Delivery (Pedidos) - Erro ao finalizar pedido com pagamento via PIX`
- `Ello Financeiro (Relatórios) - Relatório mensal exibe valores zerados após atualização`

**Regras:**
- Entre 20 e 120 caracteres
- Nunca começar com "URGENTE", "BUG" ou "ERRO"
- Específico o suficiente para identificar sem ler a descrição

---

## Passo 4 — Validar

Verificar internamente antes de gerar o template:

- [ ] Título entre 20 e 120 caracteres
- [ ] Cliente definido
- [ ] Departamento definido pelo usuário
- [ ] Prioridade definida pelo usuário
- [ ] Descrição com no mínimo 50 caracteres
- [ ] Prioridade `urgente` ou `alta` tem justificativa sugerida e aprovada pelo usuário
- [ ] Comportamento esperado e observado são distintos
- [ ] Corpo descreve só o problema — nenhuma solução/correção
- [ ] Nenhum termo técnico de dev (arquivo, rota, tabela, coluna, método, status HTTP, stack trace)
- [ ] Linguagem clara, simples e concisa, compreensível pelo suporte

Se alguma regra falhar, solicitar ajuste antes de continuar.

> ⚠️ **Não confundir com a validação de formatação HTML do Passo 7** — esta lista é só sobre conteúdo/linguagem. A validação de que o corpo real será enviado em HTML (não em texto puro) é obrigatória e está no Passo 7; nenhuma das duas dispensa a outra.

---

## Passo 5 — Exibir Template

Ler `../templates/chamado.md`. O arquivo já traz as duas versões fixas e prontas — só preencher os `[colchetes]` com os dados coletados, sem alterar a estrutura:

1. **Seção 1 (Preview em Markdown)** do template — preencher e exibir ao usuário exatamente assim, fora de bloco de código (não envolver com ```` ``` ````, senão perde a formatação).
2. **Seção 2 (Payload em HTML)** do template — preencher em paralelo (mesmos dados), mas **não mostrar ao usuário**. É o que será reaproveitado no Passo 7/8, sem reescrever.

O preview (1) é só representação visual para aprovação — nunca é o que vai para a API. O que vai para `criar_chamado` é sempre o HTML (2).

**O chamado NÃO deve ser criado neste momento.**

---

## Passo 6 — Confirmar

> "Os dados estão corretos? Deseja confirmar a abertura do chamado?"
> `[S] Sim, criar` | `[N] Cancelar` | `[E] Editar campo`

- **S:** executar criação (Passo 7 — validar HTML, depois Passo 8)
- **N:** cancelar sem criar nada
- **E:** perguntar qual campo corrigir, coletar novo valor, voltar ao Passo 5

---

## Passo 7 — Validar Formatação HTML (obrigatório, antes de enviar)

Aplicar a checklist de `checklist-html.md` sobre o HTML montado no Passo 5 (Seção 2 do template). Obrigatória, não pode ser pulada, mesmo com o conteúdo textual já validado no Passo 4.

---

## Passo 8 — Criar via MCP

Executar nesta ordem:

```
1. mcp__tomticket__buscar_cliente(q="Ello Tecnologia")
   → anotar cliente_id

2. mcp__tomticket__listar_operadores(departamento="<escolhido>")
   → localizar operador pelo nome (somente se o usuário quis se atribuir)

3. mcp__tomticket__criar_chamado(
     departamento = "<escolhido>",
     titulo       = "<gerado>",
     mensagem     = "<corpo em HTML — ver ferramentas.md — NUNCA Markdown/texto puro>",
     cliente_id   = "<id do passo 1>",
     prioridade   = "baixa | normal | alta | urgente",
     categoria    = "<escolhida>",
     operador     = "<nome, se atribuído>"
   )
```

Após criação, exibir ao usuário o número do protocolo retornado.

---

## Notas

- Cliente padrão: **Ello Tecnologia** quando não informado
- Departamento e ambiente são sempre perguntados
- O corpo do chamado deve seguir as diretrizes de formatação em `ferramentas.md`
