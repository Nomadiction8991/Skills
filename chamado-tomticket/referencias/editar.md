# Referência — Editar Chamado

Passo a passo completo para edição de chamados existentes no TomTicket.

---

## Princípios do conteúdo

Ver `regras-gerais.md` (regra #3) — aplica-se integralmente à edição, inclusive quando a correção já existe (commit feito, branch pronta, código em produção): o corpo continua descrevendo o problema em aberto, nunca a solução. Antes de exibir o antes/depois no Passo 5, revisar o chamado inteiro contra essa regra.

Se o usuário anexou um arquivo nesta conversa (ou pedir para incluir um): imagem → ver `imagens.md`; outro tipo de arquivo → ver `anexos.md`. Consultar antes de montar o novo corpo.

---

## Limitações da Ferramenta

A ferramenta `mcp__tomticket__editar_chamado` suporta edição apenas de:

- **`titulo`** — o título/assunto do chamado
- **`mensagem`** — o corpo/descrição principal do chamado

Campos como prioridade, categoria, departamento e operador **não podem ser alterados** por esta skill. Para alterar esses campos, usar a interface do TomTicket diretamente.

---

## Passo 1 — Obter Número e Intenção

O formato esperado é `editar <número> <descrição do que editar>`.

- **Número:** extrair o primeiro token numérico após "editar"
- **Intenção:** extrair o texto restante após o número — usá-lo para já saber o que o usuário quer modificar, pulando ou direcionando o Passo 3

Se o número não foi informado, perguntar:
> "Qual é o número do chamado que deseja editar?"

Se a intenção não foi informada, seguir normalmente para o Passo 3 e perguntar o que editar.

---

## Passo 2 — Buscar Dados Atuais

Consultar o chamado atual (ver `ler.md`, Passo 2 — `mcp__tomticket__ver_chamado`).

Exibir ao usuário um resumo do que foi encontrado:

```
Chamado #<número> encontrado:
Título atual   : <titulo>
Descrição atual: <primeiros 200 caracteres da mensagem>...
```

---

## Passo 3 — Identificar o que Editar

Tentar inferir a partir da intenção capturada no Passo 1 (e do resumo exibido no Passo 2):

- Menciona palavras como "título"/"assunto" → título
- Descreve o problema, sintoma ou contexto → descrição (corpo)
- Menciona os dois, ou o texto dá a entender que ambos mudam → ambos

Só perguntar se não for possível inferir com confiança ou a intenção for ambígua:

> "O que deseja editar?"
> `[1] Título` | `[2] Descrição (corpo)` | `[3] Ambos`

---

## Passo 4 — Coletar Novos Valores

### Se editar título:
Perguntar o novo título ou sugerir um melhorado com base no contexto atual.
Aplicar as mesmas regras de `abrir.md` (Passo 3 — Regras).

### Se editar descrição:
Perguntar ao usuário o que mudou ou o que precisa ser corrigido/complementado.
Montar o novo corpo completo usando a estrutura fixa de `../templates/chamado.md` (Seção 2 — Payload HTML), só trocando o conteúdo entre `[colchetes]`. O texto/Markdown (Seção 1 do template) só existe na prévia do Passo 5 para o usuário ler — nunca é o que vai para a API.

---

## Passo 5 — Exibir Antes/Depois para Revisão

Exibir o chamado **completo e inteiro** duas vezes — a versão atual e a versão nova — nunca só o trecho alterado, para o usuário conseguir comparar visualmente as duas versões inteiras.

**Formato (testado e acordado com o usuário — não tentar "melhorar" com diff/ANSI, já foram tentados e não funcionam neste chat):**

1. Uma linha cheia de 🔴 (repetir o emoji até preencher a largura, ~40x), sinalizando "começa a versão atual"
2. O chamado **atual, completo**, preenchido na Seção 1 (Preview em Markdown) de `../templates/chamado.md`
3. Uma linha cheia de 🟡, sinalizando "fim do atual / início do novo"
4. O chamado **novo, completo**, no mesmo template (Seção 1)
5. Uma linha cheia de 🟢, sinalizando "fim do novo"

Motivo de não usar cores reais (ANSI, ```diff```, etc.): já testado nesta skill — ANSI não renderiza neste chat (aparece como texto cru `[31m`), e blocos ` ```diff ` matam a formatação Markdown (negrito, listas) do conteúdo porque código não renderiza Markdown dentro. As linhas de emoji são a única forma que preserva Markdown rico E dá um marcador visual de início/meio/fim.

**A edição NÃO deve ser executada neste momento.**

---

## Passo 6 — Confirmar conteúdo

> "Os dados estão corretos? Deseja confirmar a edição?"
> `[S] Sim, salvar` | `[N] Cancelar` | `[E] Ajustar`

- **S:** executar edição (Passo 7 — validar HTML, depois Passo 8)
- **N:** cancelar sem alterar nada; informar: "Edição cancelada. Nenhuma alteração foi realizada."
- **E:** perguntar o que ajustar e voltar ao Passo 4

---

## Passo 7 — Validar Formatação HTML (obrigatório, antes de enviar)

Aplicar a checklist de `checklist-html.md` — obrigatório mesmo em edição, e não pode ser pulado.

---

## Passo 8 — Editar via MCP

```
mcp__tomticket__editar_chamado(
  numero   = "<número do chamado>",
  titulo   = "<novo título, se alterado>",     # omitir se não alterado
  mensagem = "<novo corpo completo em HTML — NUNCA Markdown/texto puro>" # omitir se não alterado
)
```

Ao menos um dos dois campos (`titulo` ou `mensagem`) deve ser enviado.

Após a edição, confirmar ao usuário: "Chamado #<número> atualizado com sucesso."

---

## Notas

- O novo corpo deve ser completo — não apenas o trecho alterado
- Seguir as diretrizes de formatação em `ferramentas.md`
