# Referência — Transferir Chamado

Passo a passo completo para transferir um chamado existente para outro departamento e/ou operador no TomTicket.

Ver `regras-gerais.md` (regra #1 — confirmação explícita antes de enviar) e (regra #2 — HTML real, nunca Markdown/texto puro no payload, quando houver mensagem).

---

## Limitações da Ferramenta

A ferramenta `mcp__tomticket__transferir_chamado` aceita:

- **`numero`** *(obrigatório)* — número do chamado
- **`departamento`** *(opcional)* — novo departamento
- **`operador`** *(opcional)* — novo responsável
- **`mensagem`** *(opcional)* — nota sobre o motivo da transferência

Pelo menos um entre `departamento` e `operador` é obrigatório — transferir sem informar nenhum dos dois não faz sentido.

---

## Passo 1 — Obter Número e Destino

O formato esperado é `transferir <número> <departamento e/ou operador de destino>`.

- **Número:** extrair o primeiro token numérico após "transferir"
- **Destino:** extrair o texto restante — usar para já inferir departamento/operador de destino, pulando ou direcionando o Passo 3

Se o número não foi informado, perguntar:
> "Qual é o número do chamado que deseja transferir?"

Se o destino não foi informado, seguir normalmente para o Passo 3 e perguntar.

---

## Passo 2 — Buscar Dados Atuais

Consultar o chamado atual (ver `ler.md`, Passo 2 — `mcp__tomticket__ver_chamado`).

Exibir ao usuário um resumo:

```
Chamado #<número> encontrado:
Departamento atual: <departamento>
Responsável atual : <operador>
```

---

## Passo 3 — Identificar Departamento e/ou Operador de Destino

Tentar inferir a partir do destino capturado no Passo 1. Só perguntar o que não for possível inferir com confiança:

Se o departamento não estiver claro, chamar `listar_departamentos()` e perguntar:
> "Para qual departamento transferir? `[1] <sugerido>` `[2] <próximo>` ..." | `[N] Manter o departamento atual`

Se o operador não estiver claro, e um departamento de destino já estiver definido (o novo ou o atual), chamar `listar_operadores(departamento="<departamento de destino>")` e perguntar:
> "Para qual operador transferir? `[1] <nome>` `[2] <nome>` ..." | `[N] Não atribuir/manter atual`

Pelo menos um dos dois (departamento ou operador) deve ficar definido ao final deste passo — se nenhum for informado, repetir a pergunta.

---

## Passo 4 — Mensagem (opcional)

Perguntar se o usuário quer registrar um motivo para a transferência:
> "Deseja adicionar uma mensagem explicando o motivo da transferência? `[S] Sim` | `[N] Não incluir mensagem"`

Se sim, montar o HTML seguindo as diretrizes de `ferramentas.md` (parágrafo simples, sem necessidade da estrutura de seções do template de criação/edição).

---

## Passo 5 — Confirmar

Exibir o resumo final e pedir confirmação:

```
Chamado #<número>
Departamento: <atual> → <novo, ou "mantém">
Responsável : <atual> → <novo, ou "mantém">
Mensagem    : <mensagem, se houver, ou "nenhuma">
```

> "Confirma a transferência? `[S] Sim` | `[N] Cancelar` | `[E] Ajustar`"

- **S:** avançar para o Passo 6 (validar HTML, se houver mensagem) e depois Passo 7
- **N:** cancelar sem alterar nada
- **E:** perguntar o que ajustar e voltar ao Passo 3

---

## Passo 6 — Validar Formatação HTML (obrigatório quando houver mensagem)

Se houver `mensagem`, aplicar a checklist de `checklist-html.md` antes de enviar.

---

## Passo 7 — Transferir via MCP

```
mcp__tomticket__transferir_chamado(
  numero      = "<número do chamado>",
  departamento = "<novo departamento, se alterado>",  # omitir se não alterado
  operador     = "<novo operador, se alterado>",       # omitir se não alterado
  mensagem     = "<HTML do motivo, se informado>"      # omitir se não informado
)
```

Após a transferência, confirmar ao usuário: "Chamado #<número> transferido com sucesso."

---

## Notas

- Ao menos um entre `departamento` e `operador` deve ser enviado
- Seguir as diretrizes de formatação em `ferramentas.md` quando houver mensagem
