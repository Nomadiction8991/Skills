Template fixo — só preencher os `[colchetes]` com os dados coletados em `../referencias/criar.md`, sem alterar a estrutura. É a prévia exibida ao usuário no terminal antes de qualquer push/criação.

---

## Preview modo único (exibir ao usuário)

```
Projeto      : [namespace/repo]
Origem       : [branch de origem]
Destino      : [branch de destino]
Responsável  : [nome | Sem responsável]

Título
[Título — 1 commit: subject do commit; 2+ commits: síntese do Passo 3 do modo único de `../referencias/criar.md`]

Descrição
[Descrição — 1 commit: body do commit, ou vazia se o commit não tem body; 2+ commits: síntese do Passo 4 do modo único de `../referencias/criar.md`]
```

## Preview modo escadinha (exibir ao usuário, um bloco por MR, ordem 1→N)

```
Push (ordem 1→N): git push -u origin [branch-1], [branch-2], ...

MR 1/N | Projeto: [namespace/repo] | [origem-1] → [destino-1] | Responsável: [nome | Sem responsável]
Título: [1 commit exclusivo: subject; 2+ commits exclusivos: síntese do Passo 3 do modo único de `../referencias/criar.md`]
Descrição: [1 commit exclusivo: body, ou (vazia — o commit não tem body); 2+ commits exclusivos: síntese do Passo 4 do modo único de `../referencias/criar.md`]

MR 2/N | Projeto: [namespace/repo] | [origem-2] → [destino-2] | Responsável: [nome | Sem responsável]
Título: [1 commit exclusivo: subject; 2+ commits exclusivos: síntese do Passo 3 do modo único de `../referencias/criar.md`]
Descrição: [1 commit exclusivo: body, ou (vazia — o commit não tem body); 2+ commits exclusivos: síntese do Passo 4 do modo único de `../referencias/criar.md`]
...
```

---

**Regras:**
- O princípio central, incluindo os casos de 1 e 2+ commits exclusivos, está em `../referencias/criar.md`; este template apenas define o formato da prévia.
- Se o usuário pedir edição (opção `[E]` na confirmação), atualizar só o campo pedido e reexibir o template inteiro de novo antes de confirmar outra vez.
