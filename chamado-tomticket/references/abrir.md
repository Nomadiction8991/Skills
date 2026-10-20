# Referência — Abrir Chamado

Passo a passo completo para abertura de chamados no TomTicket.

---

## Princípios do conteúdo

Ver `regras-gerais.md` (regra #3) — vale integralmente para o título e o corpo construídos neste fluxo.

Se o usuário anexou um arquivo nesta conversa (ou pedir para incluir um): imagem → ver `imagens.md`; outro tipo de arquivo → ver `anexos.md`. Em ambos os casos só é possível anexar/embutir depois que o chamado existir (via comentário com imagem/anexo após a criação, nunca na criação em si — `criar_chamado` não aceita arquivo e `editar_chamado` não gera anexo real).

---

## Pré-requisito

A descrição do problema deve ter pelo menos 30 caracteres. Se não tiver, interrompa e peça ao usuário que descreva melhor antes de continuar.

---

## Passo 1 — Coletar tudo sem perguntar (só trava se bloqueado)

Regra deste passo: **não perguntar nada do que der para definir ou inferir**. Buscar na API, aplicar defaults e sugestões, montar a prévia completa — o usuário corrige na prévia (`[E]`) se algo vier errado. Só parar com pergunta quando bloqueado de verdade (ver "Quando parar" abaixo). Itens independentes saem na mesma leva de chamadas.

### a) Cliente *(nunca perguntar — padrão Ello Tecnologia)*

Sempre **Ello Tecnologia**, sem perguntar. Só sai do padrão se `args`/contexto citar outro cliente explicitamente: aí chamar `buscar_cliente(q="<nome>")` e usar o melhor resultado direto na prévia (o usuário corrige se vier errado). Se a busca não retornar nada, seguir com Ello Tecnologia e sinalizar na prévia.

### b) Departamento *(sem perguntar — sugerido pela API)*

Chamar `listar_departamentos()` (o que o operador logado tem acesso) e escolher pelo contexto:

| Contexto | Departamento sugerido |
|---|---|
| Erro, bug, falha, lentidão | Suporte ou Triagem |
| Melhoria ou defeito em aplicação web (navegador) | Desenvolvimento/Web |
| Melhoria ou defeito em aplicação desktop (Windows) | Desenvolvimento |
| Implantação, onboarding, configuração inicial | IMPLANTAÇÃO |
| Representante ou parceiro comercial | UAR |
| Dúvida geral ou contexto pouco claro | Triagem |

Usar o sugerido direto na prévia (o usuário troca se quiser).

### c) Categoria *(sem perguntar — depende só do departamento)*

Chamar `listar_categorias(departamento="<escolhido>")` e usar a mais adequada ao contexto direto na prévia.

### d) Prioridade *(sem perguntar — inferida)*

Enum fixo da API (`baixa | normal | alta | urgente`) — não há tool de listagem. Estimar pelo contexto e usar direto:

| Critério | Prioridade |
|---|---|
| Sistema fora do ar, perda de dados, impacto total | `urgente` |
| Funcionalidade principal indisponível, muitos usuários | `alta` |
| Funcionalidade parcialmente afetada, workaround existe | `normal` |
| Cosmético, dúvida, melhoria, impacto mínimo | `baixa` |

### e) Descrição detalhada

Usar o texto de `args` como base, expandido com o contexto coletado. Se vago ou menor que 50 caracteres, tentar completar pelo contexto; se continuar inutilizável, ver "Quando parar" no fim do Passo 1.

### f) Passos para reproduzir *(opcional)*

Nem todo chamado precisa — só incluir a seção quando for possível descrever um passo a passo real:
- O usuário já explicou os passos na descrição, ou
- É possível inferir com confiança a partir do contexto/descrição.

Se não for nenhum dos dois casos, **não perguntar** e não inventar passos genéricos: no preview em Markdown mostrar "Não aplicável" (ver `../templates/chamado.md`), mas no HTML enviado à API **omitir a seção inteira** (`<h4>Passos para Reproduzir</h4>` + `<ol>`) — não escrever "Não aplicável" dentro do HTML.

### g) Comportamento esperado vs. observado

Derivar do contexto. Se não der para inferir os dois, deixar genérico e sinalizar na prévia (o usuário completa com `[E]`).

### h) Impacto

Estimar pelo contexto (usuários, ambiente, processos críticos). **Justificativa:** gerar a partir de prioridade + impacto + comportamento observado e incluir direto na prévia — sem rodada de aprovação separada.

### i) Responsável *(padrão: não atribuir)*

Não atribuir por padrão (`operador` vazio). Só atribuir se o contexto citar uma pessoa: aí chamar `listar_operadores(departamento=<escolhido>)`, localizar pelo nome e usar; se não encontrar, manter vazio e sinalizar na prévia.

### j) Ambiente *(padrão: Produção)*

**Produção**, salvo contexto indicando outro (homologação/teste/desenvolvimento). Sem pergunta.

### Quando parar (únicos casos que travam com pergunta)

- Descrição com menos de 30 caracteres e nada inferível (Pré-requisito acima) — pedir uma descrição melhor, uma vez só.
- Cliente citado no contexto mas não encontrado na busca — seguir com Ello Tecnologia e sinalizar na prévia (não travar por isso).

Fora disso, nunca parar para perguntar: campo incerto vai com o melhor palpite sinalizado na prévia e o usuário corrige com `[E]`.

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
- [ ] Cliente definido (padrão Ello Tecnologia, ou o citado no contexto)
- [ ] Departamento definido (API + contexto)
- [ ] Prioridade inferida com justificativa presente na prévia
- [ ] Descrição com no mínimo 50 caracteres
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
3. Exiba a prévia em Markdown como texto normal do chat (nunca dentro de `question` ou bloco de ferramenta — não fica legível), bem separada do pedido de confirmação. O HTML da Seção 2 continua sendo o payload enviado à API.

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
1. Cliente: usar o `cliente_id` já resolvido no Passo 1 (Ello Tecnologia por padrão, ou o citado no contexto) — só chamar `mcp__tomticket__buscar_cliente(q="<nome>")` se ainda não resolvido
   → anotar cliente_id

2. mcp__tomticket__listar_operadores(departamento="<escolhido>")
   → localizar operador pelo nome (somente se o Passo 1 atribuiu alguém pelo contexto; padrão é não atribuir — pule este item)

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
- Departamento e ambiente saem da API/contexto, sem perguntar
- O corpo do chamado deve seguir as diretrizes de formatação em `ferramentas.md`
