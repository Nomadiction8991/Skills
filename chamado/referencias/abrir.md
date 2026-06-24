# Referência — Abrir Chamado

Passo a passo completo para abertura de chamados no TomTicket.

---

## Pré-requisito

A descrição do problema deve ter pelo menos 30 caracteres. Se não tiver, interrompa e peça ao usuário que descreva melhor antes de continuar.

---

## Passo 1 — Coletar Informações

Faça no máximo uma pergunta por vez. Tente inferir antes de perguntar.

### a) Cliente

Padrão: **Ello Tecnologia** (não perguntar). Chame `buscar_cliente(q="Ello Tecnologia")` para obter o `cliente_id`.

### b) Departamento *(sempre perguntar)*

Sugerir com base no contexto:

| Contexto | Departamento sugerido |
|---|---|
| Erro, bug, falha, lentidão | Suporte ou Triagem |
| Melhoria ou defeito em aplicação web (navegador) | Desenvolvimento/Web |
| Melhoria ou defeito em aplicação desktop (Windows) | Desenvolvimento |
| Implantação, onboarding, configuração inicial | IMPLANTAÇÃO |
| Representante ou parceiro comercial | UAR |
| Dúvida geral ou contexto pouco claro | Triagem |

Apresentar lista completa, destacar sugestão e deixar o usuário decidir.

### c) Categoria

Usar a tabela de departamentos do SKILL.md. Sugerir a mais adequada e perguntar ao usuário.

### d) Prioridade *(inferir automaticamente)*

| Prioridade | Critério |
|---|---|
| `urgente` | Sistema fora do ar, perda de dados, impacto total |
| `alta` | Funcionalidade principal indisponível, muitos usuários |
| `normal` | Funcionalidade parcialmente afetada, workaround existe |
| `baixa` | Cosmético, dúvida, melhoria, impacto mínimo |

Se o impacto não estiver claro, perguntar: "Qual é o impacto para a operação?"

### e) Descrição detalhada

Usar o texto de `args` como base. Expandir com contexto coletado. Se vago ou menor que 50 caracteres, pedir mais detalhes.

### f) Passos para reproduzir

Tentar inferir da descrição. Se for defeito/incidente e não for possível inferir, perguntar: "Quais são os passos para reproduzir?"

### g) Comportamento esperado vs. observado

Derivar do contexto. Perguntar apenas se não for possível inferir os dois.

### h) Impacto

Estimar pelo contexto. Se não for possível, perguntar: "Quantos usuários ou processos são afetados?"

### i) Responsável *(sempre perguntar)*

> "Deseja se atribuir como responsável? `[S] Sim` `[N] Não`"

- **Sim:** chamar `listar_operadores(departamento=<escolhido>)` e localizar o operador pelo nome. Se não encontrar, informar ao usuário e deixar em branco.
- **Não:** não preencher `operador`.

### j) Ambiente *(sempre perguntar)*

> "Qual é o ambiente afetado? `[1] Produção` `[2] Homologação` `[3] Desenvolvimento`"

---

## Passo 2 — Investigar o Codebase

Antes de construir o título e o corpo, explorar o código relacionado ao módulo/tela mencionado na descrição.

**Objetivo:** enriquecer o chamado com informações técnicas reais — nomes de arquivos, rotas, componentes, tabelas, campos — tornando o conteúdo mais preciso e útil para quem for atender.

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

4. **Extrair informações técnicas** para enriquecer:
   - Nome real dos arquivos/componentes afetados
   - Nomes de tabelas e campos do banco
   - Rotas de API ou métodos HTTP envolvidos
   - Qualquer comportamento do código que explique ou contextualize o problema

### O que fazer com o que encontrar

Incorporar naturalmente no corpo do chamado:
- Na seção **Descrição**: citar o componente ou arquivo real (ex: `ConfiguracoesForm.vue`, tabela `lojas`)
- Na seção **Comportamento Observado**: referenciar a rota ou método (ex: `POST /api/loja/configuracoes`)
- Na seção **Impacto**: mencionar campos específicos afetados

> Se após a busca não for encontrado nada relevante, continuar normalmente com o que o usuário forneceu.

---

## Passo 3 — Construir o Título

Gerado automaticamente, nunca perguntar ao usuário.

**Formato:**
```
[Nome do Sistema] [Módulo] - Descrição concisa do chamado
```

**Para descobrir o nome do sistema**, ler os arquivos `.md` da raiz:
```bash
find . -maxdepth 1 -name "*.md" | sort | head -5
```

**Exemplos:**
- `Ello Delivery Pedidos - Erro ao finalizar pedido com pagamento via PIX`
- `Ello Financeiro - Relatório mensal exibe valores zerados após atualização`

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
- [ ] Prioridade definida (automática ou pelo usuário)
- [ ] Descrição com no mínimo 50 caracteres
- [ ] Prioridade `urgente` ou `alta` tem impacto justificado
- [ ] Comportamento esperado e observado são distintos

Se alguma regra falhar, solicitar ajuste antes de continuar.

---

## Passo 5 — Exibir Template

Ler `.claude/skills/chamado/templates/criar.md`, preencher com todos os dados e exibir ao usuário.

**O chamado NÃO deve ser criado neste momento.**

---

## Passo 6 — Confirmar

> "Os dados estão corretos? Deseja confirmar a abertura do chamado?"
> `[S] Sim, criar` | `[N] Cancelar` | `[E] Editar campo`

- **S:** executar criação (Passo 7)
- **N:** cancelar sem criar nada
- **E:** perguntar qual campo corrigir, coletar novo valor, voltar ao Passo 5

---

## Passo 7 — Criar via MCP

Executar nesta ordem:

```
1. mcp__tomticket__buscar_cliente(q="Ello Tecnologia")
   → anotar cliente_id

2. mcp__tomticket__listar_operadores(departamento="<escolhido>")
   → localizar operador pelo nome (somente se o usuário quis se atribuir)

3. mcp__tomticket__criar_chamado(
     departamento = "<escolhido>",
     titulo       = "<gerado>",
     mensagem     = "<corpo em Markdown — ver referencias/ferramentas.md>",
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
- O corpo do chamado deve seguir as diretrizes de formatação em `referencias/ferramentas.md`
