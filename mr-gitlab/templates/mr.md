Template fixo — só preencher os `[colchetes]` com os dados coletados em `../referencias/criar.md`, sem alterar a estrutura. É a prévia exibida ao usuário no terminal antes de criar a MR (Passo 6 de `criar.md`).

---

## Preview (exibir ao usuário)

```
Projeto      : [namespace/repo]
Origem       : [branch de origem]
Destino      : [branch de destino]
Responsável  : [nome | Sem responsável]

Título
[Título montado a partir dos commits — Passo 3]

Descrição
[Lista de commits, um por linha — Passo 4]
```

---

**Regras:**
- Título e descrição vêm dos commits (ver princípio central em `../referencias/criar.md`) — não reescrever o conteúdo técnico, só reproduzir.
- Se o usuário pedir edição (opção `[E]` na confirmação), atualizar só o campo pedido e reexibir o template inteiro de novo antes de confirmar outra vez.
