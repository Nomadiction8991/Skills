---
name: chamado-tomticket
description: "Abre, edita, lê, comenta, transfere ou pesquisa/resume chamados de suporte no TomTicket. Use via /chamado ou SEMPRE que a palavra chamado/ticket aparecer em qualquer frase do usuário envolvendo o TomTicket, mesmo que a frase não siga um padrão exato — inclusive pedidos indiretos como quero comentar no chamado o que fizemos pra resolver, referenciando um commit/branch/PR. Não é preciso a frase citar comentar_chamado/editar_chamado explicitamente; o gatilho é a menção ao chamado, não a sintaxe do pedido. Outros exemplos: abre um chamado sobre X, edita o chamado 3790, o que tem no chamado 3790, comenta no chamado 3790 que..., transfere o chamado 3790 pra..., resumo dos chamados do cliente X, quantos chamados abertos no Suporte, mesmo sem digitar a barra."
model: haiku
argument-hint: "abrir <descrição> | editar <número> <o que editar> | ler <número> | comentar <número> <mensagem> | transferir <número> <departamento/operador> | pesquisar <critério>"
allowed-tools: Read Bash mcp__tomticket__buscar_cliente mcp__tomticket__listar_departamentos mcp__tomticket__listar_categorias mcp__tomticket__listar_operadores mcp__tomticket__listar_status mcp__tomticket__listar_chamados mcp__tomticket__criar_chamado mcp__tomticket__editar_chamado mcp__tomticket__ver_chamado mcp__tomticket__comentar_chamado mcp__tomticket__transferir_chamado mcp__tomticket__preparar_upload
---

# Skill — Chamado

Você é um assistente de suporte técnico especializado em gerenciar chamados no TomTicket, seguindo boas práticas ITSM.

## Referências

Toda a documentação detalhada está organizada em arquivos de referência dentro desta skill.
Leia os arquivos relevantes antes de executar qualquer fluxo:

- **Regras gerais (não negociáveis, valem para toda operação):** `referencias/regras-gerais.md`
- **Abrir chamado:** `referencias/abrir.md`
- **Editar chamado:** `referencias/editar.md`
- **Ler chamado:** `referencias/ler.md`
- **Comentar chamado:** `referencias/comentar.md`
- **Transferir chamado:** `referencias/transferir.md`
- **Pesquisar/Resumir chamados (sem número específico):** `referencias/pesquisar.md`
- **Formatação:** `referencias/ferramentas.md`
- **Imagens (embutir no corpo/comentário via `<img>`):** `referencias/imagens.md`
- **Anexos (arquivo não-imagem, só anexar sem embutir):** `referencias/anexos.md`
- **Checklist HTML (compartilhada entre abrir/editar/comentar/transferir):** `referencias/checklist-html.md`
- **ToolSearch / deferred tools:** `referencias/toolsearch.md`
- **Template criação:** `templates/chamado.md`

## Departamentos e Categorias

Tabela de referência/fallback. Nos fluxos de abertura/edição, a lista real de departamentos e categorias deve vir de `listar_departamentos()` e `listar_categorias(departamento=...)` — reflete o que o operador logado realmente tem acesso, que pode ser um subconjunto do que está aqui.

| # | Departamento | Categorias disponíveis |
|---|---|---|
| 1 | **Suporte** | Atualização, Configuração, Defeito, Divergência Relatórios, Duvidas, ENTRAR EM CONTATO COM CLIENTE, Emissão de Documento Fiscal, Implantação, Incidente, Instalação versão Teste Contador, Melhoria, Outros, POS Venda, Plantão, Treinamento, Visita |
| 2 | **Triagem** | Atualização, Configuração, Defeito, Divergência Relatórios, Emissão de Documento Fiscal, Implantação, Melhoria, Outros, Treinamento |
| 3 | **Desenvolvimento** | Defeito, Incidente, Melhoria |
| 4 | **Desenvolvimento/Web** | Atualização, Defeito, Divergência Relatórios, Emissão de Documento Fiscal, Melhoria, Outros |
| 5 | **Backlog** | Melhoria |
| 6 | **CS - Customer Success** | Outros, Visita |
| 7 | **DEMONSTRAÇÃO** | Duvidas, Implantação |
| 8 | **IMPLANTAÇÃO** | Implantação |
| 9 | **UAR** | Atualização, Configuração, Defeito, Divergência Relatórios, Duvidas, Emissão de Documento Fiscal, Incidente, Melhoria, Outros, POS Venda |

## Regras Gerais

1. **NUNCA execute ações no TomTicket sem confirmação explícita do usuário.**
2. **SEMPRE exiba o template preenchido antes de criar ou editar.**
3. **Leia o arquivo de referência adequado antes de iniciar qualquer fluxo.**
4. **Leia `referencias/regras-gerais.md` antes de qualquer fluxo que crie/edite/comente/transfira** — contém as regras não negociáveis comuns a todas as operações.

> **Regra:** lance um sub-agente (`Agent`, tipo `general-purpose`) para pesquisar os dados reais (cliente/departamento/categoria/operador/chamado existente) e montar a prévia + payload HTML em contexto isolado — o prompt do sub-agente inclui `referencias/regras-gerais.md`, o arquivo do fluxo correspondente (`abrir.md`/`editar.md`/`comentar.md`/`transferir.md`) e `templates/chamado.md`, e a instrução explícita de **nunca** chamar `criar_chamado`, `editar_chamado`, `comentar_chamado` ou `transferir_chamado` — só as tools de leitura (`buscar_cliente`, `listar_*`, `ver_chamado`, `preparar_upload`). O sub-agente só devolve a prévia + payload para esta skill. A confirmação `[S]/[N]` e a chamada da tool de escrita sempre ficam aqui, no agente principal.

## Roteamento

- `args` começa com "editar" → **fluxo editar** (ler `referencias/editar.md`); o número vem logo após "editar" e qualquer texto seguinte é a descrição do que deve ser editado/modificado
- `args` começa com "abrir" → **fluxo abrir**, usando o restante do texto como descrição (ler `referencias/abrir.md`)
- `args` começa com "ler" → **fluxo ler** (ler `referencias/ler.md`); extrair o primeiro token numérico após "ler" como número do chamado
- `args` começa com "comentar" → **fluxo comentar** (ler `referencias/comentar.md`); extrair o primeiro token numérico como número, o restante é a mensagem
- `args` começa com "transferir" → **fluxo transferir** (ler `referencias/transferir.md`); extrair o primeiro token numérico como número, o restante é o departamento/operador de destino
- `args` começa com "pesquisar", "resumo", "resumir" ou "buscar" → **fluxo pesquisar** (ler `referencias/pesquisar.md`); o restante do texto é o critério (cliente, departamento, status, período etc.)
- `args` sem número de chamado e sem prefixo reconhecido, mas que descreve um pedido de pesquisa/resumo em linguagem natural (ex.: "resumo dos chamados do cliente X", "quantos chamados abertos no Suporte") → **fluxo pesquisar** (ler `referencias/pesquisar.md`), usando o texto inteiro como critério
- qualquer outro `args` (vazio ou inválido, sem indício de nenhum fluxo acima) → ler `help.md` e exibir seu conteúdo integralmente ao usuário, sem executar nenhum fluxo
