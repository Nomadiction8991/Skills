# Referência — Editar Chamado

Passo a passo completo para edição de chamados existentes no TomTicket.

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

Chamar `mcp__tomticket__ver_chamado(numero="<número>")` para obter o estado atual do chamado.

Exibir ao usuário um resumo do que foi encontrado:

```
Chamado #<número> encontrado:
Título atual   : <titulo>
Descrição atual: <primeiros 200 caracteres da mensagem>...
```

---

## Passo 3 — Perguntar o que Editar

> "O que deseja editar?"
> `[1] Título` | `[2] Descrição (corpo)` | `[3] Ambos`

---

## Passo 4 — Coletar Novos Valores

### Se editar título:
Perguntar o novo título ou sugerir um melhorado com base no contexto atual.
Aplicar as mesmas regras do título de criação (20–120 caracteres, descritivo, sem "URGENTE"/"BUG" no início).

### Se editar descrição:
Perguntar ao usuário o que mudou ou o que precisa ser corrigido/complementado.
Montar o novo corpo completo em Markdown seguindo `referencias/ferramentas.md`.
**Nunca apagar o rodapé de IA — sempre incluí-lo no novo corpo.**

---

## Passo 5 — Exibir Antes/Depois para Revisão

Exibir ao usuário um diff simples com o que vai mudar:

```
Edição do chamado #[número]

  Título
    Antes  : <título atual>
    Depois : <novo título ou "sem alteração">

  Descrição
    Antes  : <trecho relevante atual ou "sem alteração">
    Depois : <trecho modificado ou "sem alteração">
```

**A edição NÃO deve ser executada neste momento.**

---

## Passo 6 — Confirmar conteúdo

> "Os dados estão corretos? Deseja confirmar a edição?"
> `[S] Sim, salvar` | `[N] Cancelar` | `[E] Ajustar`

- **S:** avançar para o Passo 6B (confirmação de segurança)
- **N:** cancelar sem alterar nada
- **E:** perguntar o que ajustar e voltar ao Passo 4

---

## Passo 6B — Confirmação de segurança (obrigatória)

Antes de executar a edição, exibir obrigatoriamente a seguinte mensagem de alerta, com destaque visual:

```
⚠️  ATENÇÃO — CONFIRMAÇÃO FINAL ANTES DE SALVAR

Você está prestes a EDITAR o seguinte chamado:

  Número : #<número do chamado>
  Título : <título atual do chamado>

Verifique com cuidado se o número e o título acima
correspondem ao chamado que você deseja alterar.
Edições não podem ser desfeitas automaticamente.

Tem certeza de que deseja prosseguir?
[S] Sim, tenho certeza — salvar agora   |   [N] Cancelar
```

- **S:** executar edição (Passo 7)
- **N:** cancelar sem alterar nada; informar: "Edição cancelada. Nenhuma alteração foi realizada."

---

## Passo 7 — Editar via MCP

```
mcp__tomticket__editar_chamado(
  numero   = "<número do chamado>",
  titulo   = "<novo título, se alterado>",     # omitir se não alterado
  mensagem = "<novo corpo completo em Markdown>" # omitir se não alterado
)
```

Ao menos um dos dois campos (`titulo` ou `mensagem`) deve ser enviado.

Após a edição, confirmar ao usuário: "Chamado #<número> atualizado com sucesso."

---

## Notas

- O rodapé de IA deve sempre estar presente no corpo editado
- O novo corpo deve ser completo — não apenas o trecho alterado
- Seguir as diretrizes de formatação em `referencias/ferramentas.md`
