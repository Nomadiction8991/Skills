# Referência — Anexos em Chamados (arquivos não-imagem)

Como decidir quando anexar um arquivo enviado pelo usuário a um chamado (abertura, edição ou
comentário) e como executar o upload tecnicamente. Vale para qualquer arquivo que **não** seja
imagem (pdf, log, doc, planilha, zip, etc.) — para imagem, ver `imagens.md` (embutida no HTML, não
só anexada).

---

## Quando isso se aplica

- Usuário anexa um arquivo na conversa **e** pede explicitamente para incluí-lo no chamado →
  sempre seguir este fluxo (após confirmação padrão).
- Usuário anexa um arquivo sem pedir explicitamente, mas o arquivo é pertinente ao chamado
  (log de erro, planilha com os dados divergentes, documento citado na conversa) → **sugerir** o
  anexo, nunca anexar sem perguntar.
- Arquivo irrelevante ao chamado → não sugerir, não anexar.

A avaliação de pertinência é sua (do assistente) — mas a decisão de anexar é sempre do usuário.

---

## Regra de confirmação

Vale a regra geral #1 (`regras-gerais.md`) sem exceção: nunca anexar arquivo sem confirmação
explícita, mesmo achando que é uma boa ideia.

> "Esse arquivo parece relevante para o chamado — quer que eu anexe no [chamado / comentário]?
> `[S] Sim` `[N] Não`"

---

## Passo 1 — Localizar o arquivo

O arquivo enviado pelo usuário nesta conversa chega como anexo — localizar o caminho local a
partir do contexto da mensagem/anexo antes de seguir. Sem o caminho local não é possível fazer
upload.

> ⚠️ **TESTADO E CONFIRMADO (2026-07-28):** um arquivo/imagem colado direto no chat/IDE não vem
> necessariamente com um caminho local acessível via Bash. Para imagem colada (clipboard), ver o
> atalho `wl-paste` documentado em `imagens.md`, Passo 1 — tentar isso primeiro quando aplicável
> (Wayland/KDE). Para outros tipos de arquivo, ou quando o clipboard não tiver o conteúdo, não
> assumir que dá pra localizar sozinho — perguntar o caminho ao usuário antes de prosseguir.

---

## Passo 2 — Escolher a forma de upload

| Situação | Forma |
|---|---|
| Arquivo pequeno | `arquivos` do `comentar_chamado` (base64) |
| Arquivo grande | `preparar_upload` + `curl` (Passo 3) + `comentar_chamado(uploads=...)` |

Ambas as formas só existem em `comentar_chamado` — `criar_chamado`/`editar_chamado` não têm
parâmetro de arquivo nenhum (ver Passo 4). Anexo em abertura/edição sempre vira um comentário à
parte.

---

## Passo 3 — Upload direto (`preparar_upload`)

Exige um `numero` de chamado **já existente** — não é possível fazer upload antes do chamado
existir.

```
mcp__tomticket__preparar_upload(numero="<numero>")
→ retorna url, csrf, cookie, upload_id
```

```bash
curl -s -X POST "<url>" -H "X-Xsrf-Token-Fe: <csrf>" -b "<cookie>" -F "file=@<caminho_local_do_arquivo>"
```

Capturar o campo `file` do JSON retornado pelo curl — vai ser usado em `uploads=[file, ...]`.

---

## Passo 4 — Fluxo por operação

### Comentário (mais simples — chamado já existe)

- Arquivo pequeno: `comentar_chamado(numero, mensagem, arquivos=[{...}])` (base64, Passo 2)
- Ou: `preparar_upload(numero)` → curl → `comentar_chamado(numero, mensagem, upload_id=..., uploads=[file])`

### Abertura (`criar_chamado`) e Edição (`editar_chamado`)

`criar_chamado` não tem parâmetro de arquivo, e **`editar_chamado` também não** (só aceita
`numero`/`titulo`/`mensagem` — não existe `uploads`/`arquivos` nessa tool, conferir schema real
antes de assumir o contrário). Só `comentar_chamado` aceita arquivo (`arquivos` ou
`upload_id`+`uploads`).

Logo, em abertura ou edição, **não existe forma de anexar arquivo ao corpo do chamado em si** —
o caminho é sempre um comentário adicional depois que o chamado existe:

1. `criar_chamado(...)` ou `editar_chamado(...)` normalmente, sem anexo, seguindo `abrir.md`/`editar.md`
2. Anotar/confirmar o `numero`
3. Anexar via comentário — mesmo fluxo da seção "Comentário" acima:
   `preparar_upload(numero)` → curl → `comentar_chamado(numero, mensagem="<mensagem indicando o anexo>", upload_id=..., uploads=[file])`, ou `arquivos=[{...}]` se pequeno

Avisar o usuário que o anexo vai como comentário, não dentro do corpo/mensagem principal do chamado.

---

## Notas

- Nunca anexar arquivo sem confirmação explícita do usuário (regra geral #1).
- Anexo genérico nunca é embutido no corpo/HTML — só imagem é (ver `imagens.md`).
