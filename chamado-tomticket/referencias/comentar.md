# Referência — Comentar Chamado

Passo a passo completo para adicionar comentários/respostas em chamados no TomTicket.

Ver `regras-gerais.md` (regra #2 — HTML real, nunca Markdown/texto puro no payload) e (regra #1 — confirmação explícita antes de enviar).

Se o usuário anexou um arquivo nesta conversa (ou pedir para incluir um): imagem → ver `imagens.md`; outro tipo de arquivo → ver `anexos.md`. Consultar antes de montar o comentário.

---

## Fluxo

### Passo 1 — Validar Número e Mensagem

O formato esperado é `comentar <número> <mensagem>`.

Extrair o primeiro token numérico como número do chamado. O restante é a mensagem.

Se faltar número:
> "Qual o número do chamado?"

Se faltar mensagem:
> "Qual o conteúdo do comentário?"

### Passo 2 — Definir Visibilidade (`interno`)

**Padrão recomendado desta skill: nota interna (`interno=true`)** — mesmo a tool tendo `interno=false` (visível ao cliente) como default técnico. Só usar `interno=false` quando o pedido deixar claro que é resposta direta ao cliente.

Se o pedido do usuário já deixar isso claro (ex.: "comenta como nota interna...", "responde o cliente dizendo..."), usar o valor já indicado, sem perguntar de novo.

Caso contrário, perguntar **como escolha de múltipla escolha, nunca junto com a pergunta de envio do Passo 5**, com nota interna já marcada como recomendada:

> "Esse comentário é: `[1] Nota interna (invisível ao cliente) — recomendado` `[2] Resposta visível ao cliente`"

Essa pergunta resolve só o parâmetro `interno` — **não** é a confirmação de envio (regra #1 de `regras-gerais.md`). As duas perguntas são sempre feitas em momentos separados: esta aqui define visibilidade: `[1]`/`[2]`; a do Passo 5 confirma o conteúdo final: `[S]`/`[N]`/`[E]`.

### Passo 3 — Consultar o Chamado

Consultar o chamado atual (ver `ler.md`, Passo 2 — `mcp__tomticket__ver_chamado`) para confirmar que existe e exibir contexto ao usuário.

### Passo 4 — Melhorar a Mensagem

Com base no contexto do chamado (lido no Passo 3) e na mensagem bruta do usuário, reescrever o comentário de forma profissional e clara, seguindo as diretrizes de `ferramentas.md` **e os itens 3/4 de `regras-gerais.md` (regra #3)** — sem hash de commit, nome de branch, nome de arquivo ou jargão técnico, mesmo quando o usuário mandou a mensagem bruta cheia desses detalhes (ex.: colando a mensagem de commit direto). Traduzir causa/efeito pra linguagem que a equipe de suporte entende, mesmo em nota interna.

### Passo 5 — Exibir Prévia para Aprovação

Mostrar ao usuário o comentário melhorado **em markdown/texto legível** (sem HTML), para que ele possa ler o conteúdo facilmente. O comentário real enviado no Passo 7 continuará sendo em HTML, seguindo as diretrizes de `ferramentas.md`.

```
Comentário no chamado #[número] ([Nota interna | Visível ao cliente], definido no Passo 2)

[Comentário renderizado em markdown/texto — sem tags HTML]

```

> "Deseja enviar este comentário? `[S] Sim` | `[N] Cancelar` | `[E] Editar mensagem`"

**Regras de conversão HTML → markdown/texto para a prévia:**
- `<h4>` → `### ` ou **negrito**
- `<ul>/<li>` → `- item`
- `<ol>/<li>` → `1. item`
- `<strong>` → `**negrito**`
- `<code>/<pre>` → `` `código` `` / ``` ```código``` ```
- `<span style="color: ...">` → manter texto sem a cor
- `<p>` → parágrafo
- `<blockquote>` → `> citação`
- `<hr>` → `---`
- `<table>` de galeria de imagens (ver `../templates/galeria-imagens.md`) → lista numerada, uma linha por imagem, com breve descrição do que ela mostra (nunca colar a tabela/HTML na prévia)
- `<br>` → quebra de linha

### Passo 6 — Validar Formatação HTML (obrigatório, antes de enviar)

Aplicar a checklist de `checklist-html.md` sobre o HTML montado — obrigatório, não pode ser pulado.

### Passo 7 — Enviar

Enviar o comentário em **HTML**, seguindo as diretrizes de formatação de `ferramentas.md` (que a prévia em markdown do Passo 5 representa visualmente), passando o `interno` definido no Passo 2:

```
mcp__tomticket__comentar_chamado(numero="<número>", mensagem="<HTML do comentário>", interno=<true|false, do Passo 2>)
```

Confirmar ao usuário: "Comentário adicionado ao chamado #[número] com sucesso."

---

## Notas

- Comentários são públicos (visíveis ao cliente) por padrão (`interno=false`) — ver Passo 2 para definir o contrário
- A pergunta de visibilidade (Passo 2) e a confirmação de envio (Passo 5) são sempre perguntas separadas, nunca combinadas numa única mensagem — ver regra #1 de `regras-gerais.md`
- Seguir as diretrizes de formatação em `ferramentas.md`
