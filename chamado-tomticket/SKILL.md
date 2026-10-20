---
name: chamado-tomticket
description: "Abre, edita, lê, comenta, transfere ou pesquisa/resume chamados de suporte no TomTicket. Use via /chamado ou SEMPRE que a palavra chamado/ticket aparecer em qualquer frase do usuário envolvendo o TomTicket, mesmo que a frase não siga um padrão exato — inclusive pedidos indiretos como quero comentar no chamado o que fizemos pra resolver, referenciando um commit/branch/PR. Não é preciso a frase citar comentar_chamado/editar_chamado explicitamente; o gatilho é a menção ao chamado, não a sintaxe do pedido. Outros exemplos: abre um chamado sobre X, edita o chamado 3790, o que tem no chamado 3790, comenta no chamado 3790 que..., transfere o chamado 3790 pra..., resumo dos chamados do cliente X, quantos chamados abertos no Suporte, mesmo sem digitar a barra. Do NOT use for git commits or MRs that only cite a ticket number — use /commit or /gitlab."
effort: low
model: haiku
argument-hint: "abrir <descrição> | editar <número> <o que editar> | ler <número> | comentar <número> <mensagem> | transferir <número> <departamento/operador> | pesquisar <critério>"
allowed-tools: Read Bash mcp__tomticket__buscar_cliente mcp__tomticket__listar_departamentos mcp__tomticket__listar_categorias mcp__tomticket__listar_operadores mcp__tomticket__listar_status mcp__tomticket__listar_chamados mcp__tomticket__criar_chamado mcp__tomticket__editar_chamado mcp__tomticket__ver_chamado mcp__tomticket__comentar_chamado mcp__tomticket__transferir_chamado mcp__tomticket__alterar_status_chamado mcp__tomticket__preparar_upload mcp__gitlab__list_merge_requests mcp__gitlab__get_merge_request
---

# Skill — Chamado

Você é um assistente de suporte técnico especializado em gerenciar chamados no TomTicket, seguindo boas práticas ITSM.

## Referências

Toda a documentação detalhada está organizada em arquivos de referência dentro desta skill.
Leia os arquivos relevantes antes de executar qualquer fluxo:

- **Regras gerais (não negociáveis, valem para toda operação):** `references/regras-gerais.md`
- **Abrir chamado:** `references/abrir.md`
- **Editar chamado:** `references/editar.md`
- **Ler chamado:** `references/ler.md`
- **Comentar chamado:** `references/comentar.md`
- **Transferir chamado:** `references/transferir.md`
- **Pesquisar/Resumir chamados (sem número específico):** `references/pesquisar.md`
- **Formatação:** `references/ferramentas.md`
- **Imagens (embutir no corpo/comentário via `<img>`):** `references/imagens.md`
- **Anexos (arquivo não-imagem, só anexar sem embutir):** `references/anexos.md`
- **Checklist HTML (compartilhada entre abrir/editar/comentar/transferir):** `references/checklist-html.md`
- **ToolSearch / deferred tools:** `references/toolsearch.md`
- **Template criação:** `templates/chamado.md`
- **Template galeria de imagens:** `templates/galeria-imagens.md`
- evals/evals.json — evals (prompt → saída esperada + assertions, sem escrita real); `evals/run.md` — como rodar

## Departamentos e Categorias

Sempre lidos ao vivo via `listar_departamentos()` e `listar_categorias()` — sem tabela fixa nesta skill (a API reflete o acesso real do operador).

## Regras Gerais (resumo — fonte única em `references/regras-gerais.md`)

1. **NUNCA execute ações no TomTicket sem confirmação explícita do usuário.**
2. **SEMPRE exiba o template preenchido antes de criar ou editar.**
3. **Leia o arquivo de referência adequado antes de iniciar qualquer fluxo.**

> **Regra:** lance um sub-agente isolado de propósito geral (o tipo disponível no ambiente) para pesquisar os dados reais e montar prévia + payload HTML em contexto isolado — nunca chama tools de escrita, só leitura; confirmação `[S]/[N]` e escrita ficam no agente principal. Detalhe em `references/regras-gerais.md`.

## Roteamento

- `args` começa com "editar" → **fluxo editar** (ler `references/editar.md`); o número vem logo após "editar" e qualquer texto seguinte é a descrição do que deve ser editado/modificado
- `args` começa com "abrir" → **fluxo abrir**, usando o restante do texto como descrição (ler `references/abrir.md`)
- `args` começa com "ler" → **fluxo ler** (ler `references/ler.md`); extrair o primeiro token numérico após "ler" como número do chamado
- `args` começa com "comentar" → **fluxo comentar** (ler `references/comentar.md`); extrair o primeiro token numérico como número, o restante é a mensagem
- pedido em linguagem natural para comentar/enviar a MR do trabalho atual no chamado, sem número ou mensagem explícitos (ex.: "comentar MR no chamado", "manda a MR pro chamado") → **fluxo comentar** (ler `references/comentar.md`); resolver chamado e MR pelo contexto do trabalho atual
- `args` começa com "transferir" → **fluxo transferir** (ler `references/transferir.md`); extrair o primeiro token numérico como número, o restante é o departamento/operador de destino
- `args` começa com "pesquisar", "resumo", "resumir" ou "buscar" → **fluxo pesquisar** (ler `references/pesquisar.md`); o restante do texto é o critério (cliente, departamento, status, período etc.)
- `args` sem número de chamado e sem prefixo reconhecido, mas que descreve um pedido de pesquisa/resumo em linguagem natural (ex.: "resumo dos chamados do cliente X", "quantos chamados abertos no Suporte") → **fluxo pesquisar** (ler `references/pesquisar.md`), usando o texto inteiro como critério
- qualquer outro `args` (vazio ou inválido, sem indício de nenhum fluxo acima) → ler `help.md` e exibir seu conteúdo integralmente ao usuário, sem executar nenhum fluxo

## Exemplo

- Pedido: `/chamado ler 3790`.
- Ação: leia `references/ler.md` e consulte o chamado.
- Resultado: apresente os dados sem alterar o chamado.

## Troubleshooting

- MCP TomTicket desconectado: informe a indisponibilidade e peça reconexão; não reporte operação concluída.
- Número de chamado inexistente: informe que não foi localizado e peça confirmação do número antes de prosseguir.
