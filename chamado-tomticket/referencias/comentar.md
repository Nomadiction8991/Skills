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

Antes de perguntar em aberto, vasculhar só o trabalho em curso em busca do número: a branch atual (`git branch --show-current`) e os subjects/bodies dos commits recentes do trabalho atual (`git log --oneline -10`). Só conta como candidato o número em formato explícito de chamado (`tt-N`, `#N`, "chamado N", "ticket N", "ttN") presente na branch atual ou nesses commits — número solto sem formato (porta, quantidade, trecho de versão) NÃO conta, e marcador vindo só de branch antiga/arquivada NÃO conta; nesses casos seguir como "não achou nada" (perguntar em aberto). Se achar um único candidato explícito, ler o chamado (`ver_chamado`) e só assumir o número sem perguntar se houver coincidência em termos específicos do assunto do chamado com o trabalho (não basta semelhança genérica tipo "erro ao salvar"); sem isso, propor ("O chamado é o #N? `[S]`/`[N]`"). Se houver mais de um candidato, ler cada um (`ver_chamado`), propor a lista ordenada com `[S]`/`[N]` e nunca assumir nenhum direto. Se houver dúvida ou divergência, propor em vez de perguntar em aberto; só perguntar em aberto se não achar nada. Se assumir o número pela detecção, sinalizar na prévia do Passo 5.

Se faltar mensagem:
> "Qual o conteúdo do comentário?"

### Passo 2 — Visibilidade (`interno`)

**Sempre nota interna (`interno=true`), sem perguntar** — mesmo a tool tendo `interno=false` (visível ao cliente) como default técnico. Só usar `interno=false` quando o pedido disser explicitamente que é resposta direta ao cliente (ex.: "responde o cliente dizendo...").

A confirmação de envio do Passo 5 (regra #1 de `regras-gerais.md`) continua existindo normalmente.

### Passo 3 — Consultar o Chamado

Consultar o chamado atual (ver `ler.md`, Passo 2 — `mcp__tomticket__ver_chamado`) para confirmar que existe e exibir contexto ao usuário.

### Passo 4 — Melhorar a Mensagem

Com base no contexto do chamado (lido no Passo 3) e na mensagem bruta do usuário, reescrever o comentário de forma profissional e clara, seguindo as diretrizes de `ferramentas.md` **e os itens 3/4 de `regras-gerais.md` (regra #3)** — sem hash de commit, nome de branch, nome de arquivo ou jargão técnico, mesmo quando o usuário mandou a mensagem bruta cheia desses detalhes (ex.: colando a mensagem de commit direto). Traduzir causa/efeito pra linguagem que a equipe de suporte entende, mesmo em nota interna.

**Um chamado, um comentário (padrão):** quando a mensagem bruta citar N commits, branches ou MRs do mesmo chamado, montar **um único comentário consolidado** descrevendo o conjunto do que foi feito — nunca um comentário por commit/branch/MR. A divisão da skill de commit (1 branch = 1 MR = 1 commit) não se transfere para cá. Só montar mais de um comentário se o usuário pedir explicitamente ("dois comentários", "separa em..."). **Tempo verbal pelo estado real:** o padrão é contar o que foi mexido como trabalho feito ainda fora de produção (branches pendentes de merge na main não estão valendo) — sem mencionar que o trabalho foi dividido. Só redigir como "já valendo" se o usuário disser explicitamente que foi para a main/deploy.

**Comentário sobre MRs:** quando o comentário for sobre MRs (links de revisão), buscar os dados no GitLab via MCP — via primária (`mcp__gitlab__list_merge_requests` para listar/buscar, `mcp__gitlab__get_merge_request` para detalhes — título, `web_url`, `source_branch`, `target_branch`, estado) — ou `glab` (CLI do GitLab via Bash) só quando o MCP estiver indisponível; antes de usar o `glab`, validar acesso (`glab auth status` + host/projeto) e parar se deslogado — nunca inventar título/link do histórico da conversa. Listar só MRs abertas (`state=opened`), escopadas ao projeto do trabalho atual (remote/repo atual), mais os ancestrais concluídos necessários à cadeia — base já merged aparece como nó concluído, nunca some para não orfanar a cadeia — e montar a ordem de dependência ligando os elos (`source_branch` de uma = `target_branch` da seguinte) a partir da main; títulos vêm como estão das MRs, sem reescrever. Antes de montar, vincular cada MR ao chamado pela marca do chamado nas branches/commits das MRs (`tt-N`), além do número (regra do Passo 1); se divergir, parar e perguntar. Formato: linha de apresentação ("Seguem os MRs referentes a este chamado, aguardando revisão:") + árvore em ordem de dependência a partir da main — manter as "perninhas" (`└──`) que mostram que uma depende da outra, nunca lista simples — com o título de cada MR como link clicável: na prévia `[Título](url)`, no envio `<a target="_blank" href="url">Título</a>` dentro de lista aninhada (`<ul>` dentro de `<li>`, recuo preservado). Exemplo (prévia):
```
Seguem os MRs referentes a este chamado, aguardando revisão:

- main
  - └── [feat(base): cria estrutura](https://gitlab.exemplo.com/grupo/repo/-/merge_requests/1)
    - └── [feat(meio): usa a base](https://gitlab.exemplo.com/grupo/repo/-/merge_requests/2)
      - └── [chore(topo): ajusta texto](https://gitlab.exemplo.com/grupo/repo/-/merge_requests/3)
```
(No envio, cada `[Título](url)` vira `<a target="_blank" href="url">Título</a>` em lista aninhada — exemplo de 2 níveis:)
```html
<ul>
  <li>main
    <ul>
      <li><a target="_blank" href="https://gitlab.exemplo.com/grupo/repo/-/merge_requests/1">feat(base): cria estrutura</a>
        <ul>
          <li><a target="_blank" href="https://gitlab.exemplo.com/grupo/repo/-/merge_requests/2">feat(meio): usa a base</a></li>
        </ul>
      </li>
    </ul>
  </li>
</ul>
```

### Passo 5 — Exibir Prévia para Aprovação

Mostrar ao usuário o comentário melhorado **em markdown/texto legível** (sem HTML), para que ele possa ler o conteúdo facilmente. O comentário real enviado no Passo 7 continuará sendo em HTML, seguindo as diretrizes de `ferramentas.md`.

**Preview físico (obrigatório):** junto com a prévia em Markdown abaixo, gerar `/tmp/chamado-preview.html` e abrir automaticamente seguindo `preview-html.md` (agente principal, via Bash) — antes de pedir confirmação.

```
Comentário no chamado #[número] ([Nota interna | Visível ao cliente], definido no Passo 2)

[Comentário renderizado em markdown/texto — sem tags HTML]

```
Se o número foi assumido pela detecção automática do Passo 1 (não digitado pelo usuário), sinalizar na prévia — ex. linha extra "número detectado das branches/commits — confirme que é este chamado" — em vez de exibi-lo como se tivesse sido digitado. Só quando assumido, não quando digitado.

> "Deseja enviar este comentário? `[S] Sim` | `[N] Cancelar` | `[E] Editar mensagem`"

Quando o número foi autodetectado (não digitado), trocar a pergunta genérica pela reconfirmação explícita do número junto ao envio: "Confirme o chamado #N — é este? `[S] Sim, é este e enviar` | `[N] Cancelar` | `[E] Editar`". Quando digitado, manter a pergunta atual.

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
- `<a target="_blank" href="url">Título</a>` → `[Título](url)`
- lista aninhada de MRs (`<ul>` dentro de `<li>`) → recuo + `└──` na prévia

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

- Comentários saem sempre como nota interna (`interno=true`) — ver Passo 2; a confirmação de envio (Passo 5) continua obrigatória — ver regra #1 de `regras-gerais.md`
- Seguir as diretrizes de formatação em `ferramentas.md`
