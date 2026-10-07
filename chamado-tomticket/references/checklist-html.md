# Referência — Checklist de Validação HTML (compartilhado)

Checklist obrigatória antes de qualquer chamada de escrita (`criar_chamado`, `editar_chamado`, `comentar_chamado`, `transferir_chamado`) que envie `mensagem`. Não pode ser pulada, mesmo quando o conteúdo textual já foi validado antes (ver `regras-gerais.md`, regra #2). Formatação errada não dá erro de API — o chamado é criado/editado/comentado "com sucesso" mesmo em texto puro, e o problema só aparece se alguém abrir o chamado no navegador.

Antes de enviar, conferir o HTML montado:

- [ ] `mensagem` é HTML de verdade — nenhuma linha tipo `"Descrição\n..."` ou `"Passos para Reproduzir\n1. ..."` em texto puro
- [ ] Nenhum `<h4>`/heading com `style` direto (sempre `<span>` interno) — só se aplica quando houver heading no conteúdo
- [ ] Nenhum asterisco de Markdown (`**`, `-`, `1.`) sobrou dentro do HTML — o Markdown do preview é só visual, não vai para a API

Se qualquer item falhar, corrigir o HTML antes de prosseguir — não enviar e corrigir depois.

**Adicional só para `criar_chamado`/`editar_chamado`** (corpo estruturado pelo template, não se aplica a comentário/transferência):

- [ ] Estrutura idêntica à Seção 2 (Payload HTML) de `../templates/chamado.md` — nenhuma seção reescrita fora do padrão
- [ ] Seções não aplicáveis (ex.: Passos para Reproduzir, rótulo ad-hoc) foram **omitidas por inteiro**, não deixadas com "Não aplicável" dentro do HTML
