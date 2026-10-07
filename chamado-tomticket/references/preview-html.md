# Referência — Preview HTML físico (`/tmp/chamado-preview.html`)

Preview físico do HTML que vai para a API, para o usuário ver com formatação real antes de confirmar. Complementa (não substitui) a prévia em Markdown no terminal.

## Regras

- **Sempre gerar**, em todo fluxo que monta `mensagem` em HTML (abrir, editar, comentar, transferir com mensagem) — logo após montar o HTML e antes de pedir `[S]`/`[N]`, junto com a prévia em Markdown.
- **Arquivo fixo, sempre sobrescrito:** `/tmp/chamado-preview.html` — nunca criar outro nome, nunca versionar, nunca colocar dentro do repo da skill (evita sujeira no git).
- **Executado só pelo agente principal, via Bash** — o sub-agente (quando houver) só devolve prévia + payload em contexto isolado e nunca chama escrita; quem escreve/abre o preview é sempre o agente principal.
- **Nunca bloquear** na abertura: abrir destacado (`&`, `disown` quando aplicável) e seguir para a pergunta `[S]`/`[N]` mesmo se a abertura falhar — nesse caso só informar o caminho.

## Passo 1 — Escrever o arquivo (Bash, heredoc quoted)

Envolver o payload HTML (Seção 2 do template, ou HTML do comentário/motivo) neste invólucro mínimo e sobrescrever o arquivo com heredoc quoted (o `'HTML'` quoted evita expansão de `$`, crases e `!` do payload):

```bash
cat > /tmp/chamado-preview.html <<'HTML'
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="utf-8">
<title>Preview do chamado</title>
</head>
<body style="font-family: sans-serif; max-width: 800px; margin: 24px auto; padding: 0 16px;">
<PAYLOAD_HTML_AQUI>
</body>
</html>
HTML
```

- **Abrir/editar:** `<PAYLOAD_HTML_AQUI>` = corpo completo (Seção 2 de `../templates/chamado.md` já preenchida).
- **Editar (antes/depois):** empilhar as duas versões no mesmo arquivo — versão atual, depois `<hr>` com legendas, depois versão nova — para corresponder ao antes/depois textual do Passo 5 de `editar.md`.
- **Comentar/transferir:** `<PAYLOAD_HTML_AQUI>` = HTML do comentário/motivo.

## Passo 2 — Abrir automaticamente (destacado, com fallback)

```bash
(xdg-open /tmp/chamado-preview.html >/dev/null 2>&1 & disown) || (open /tmp/chamado-preview.html >/dev/null 2>&1 &) || (start "" "/tmp/chamado-preview.html" >/dev/null 2>&1 &); echo "preview: /tmp/chamado-preview.html"
```

- Linux usa `xdg-open`; macOS `open`; Windows (Git Bash) `start`. A cadeia tenta em ordem e nunca falha o fluxo.
- Na mensagem ao usuário, avisar junto da prévia em Markdown: "Preview físico aberto em `/tmp/chamado-preview.html` (sobrescrito a cada preview)."
