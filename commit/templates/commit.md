Template para montar a mensagem de commit (Conventional Commits) — só preencher os `[colchetes]`, sem alterar a estrutura. Ver `../referencias/conventional-commits.md` para a especificação completa e mais exemplos, e `../referencias/regras-gerais.md` (não negociáveis, valem para todo preenchimento abaixo).

> **Projeto Ello?** Em projetos do Ello ERP (detecção no Passo 0 de `../referencias/fluxo.md`) use `../referencias/ello.md` e `../templates/ello-commit.md` — o subject não usa tipo e o corpo é opcional.

```
[tipo]([escopo opcional]): [descrição curta, no imperativo, até 80 caracteres]

[corpo — o quê mudou e por quê, não como; conciso, até ~400 caracteres; parágrafos separados por linha em branco]

[rodapé opcional — BREAKING CHANGE: ..., Refs: #123]
```

**Tipos comuns:** `feat`, `fix`, `build`, `chore`, `ci`, `docs`, `style`, `refactor`, `perf`, `test`.

**Regras rápidas ao preencher:**
- `[tipo]` reflete a natureza real da mudança (ver regra #2 e #3 de `../referencias/conventional-commits.md`) — se o diff tiver mudanças de natureza distinta, considerar dividir em vários commits em vez de escolher um tipo genérico.
- `[escopo opcional]` é um substantivo entre parênteses descrevendo a área do código (ex.: `feat(parser): ...`) — omitir se não agregar clareza.
- `[descrição curta]` é imperativa ("adiciona", "corrige", "remove"), sem ponto final, resume o commit inteiro; máximo 80 caracteres (regra geral #6).
- `[corpo]`: ver `../referencias/regras-gerais.md` (regras #1, #2 e #7) — sempre obrigatório, resumindo o quê mudou e por quê em linguagem simples, sem jargão técnico, conciso (até ~400 caracteres).
- Breaking change: usar `!` logo antes dos dois-pontos do tipo/escopo (`feat(api)!: ...`) e/ou rodapé `BREAKING CHANGE: <descrição>`.
- Rodapé de atribuição de IA: ver `../referencias/regras-gerais.md` (regra #3) — nunca incluir.
- Chamado vinculado do TomTicket: ver `../referencias/regras-gerais.md` (regra #5) — quando houver, acrescentar `Refs: #<número>` e, na linha de baixo, `<Título do chamado> (<link>)`.

**Exemplo preenchido (sem chamado vinculado):**
```
fix(busca): corrige resultado antigo aparecendo por cima do mais recente

Quando o usuário digitava rápido, uma busca antiga podia terminar depois
da mais nova e sobrescrever o resultado certo na tela. Agora só o
resultado da busca mais recente é exibido.

Refs: #123
```

**Exemplo preenchido (com chamado vinculado):**
```
fix(busca): corrige resultado antigo aparecendo por cima do mais recente

Quando o usuário digitava rápido, uma busca antiga podia terminar depois
da mais nova e sobrescrever o resultado certo na tela. Agora só o
resultado da busca mais recente é exibido.

Refs: #3790
Consolidação de campos duplicados entre Configurações e Dados da Empresa (https://console.tomticket.com/dashboard/ticket/history/735d767986d27189711e65c10a42851c)
```
