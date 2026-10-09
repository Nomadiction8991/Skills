Template fixo — preencher os `[colchetes]` com os dados coletados em `../references/criar.md`. É a prévia exibida ao usuário no terminal antes de qualquer push ou criação.

---

## Preview modo único (exibir ao usuário)

```
Projeto | Origem → Destino | Assignee | Reviewer
[namespace/repo] | [branch de origem] → [branch de destino] | [nome | Sem responsável] | [nome | Sem reviewer]

Título: [tipo/contexto-curto-tt-XXXX]

Descrição:
[body literal do(s) commit(s), sem reescrever; se não houver body, omitir este trecho]

**Dor:** [detalhado, pode ter várias linhas]

**Repro:** [passo 1 + passo 2 + erro, pode ter várias linhas]

**Por que assim:** [por que essa solução e não outra, pode ter várias linhas]

**O que:** [detalhado, pode ter várias linhas]

**Como testar:** [comando + clique, pode ter várias linhas]

**Prova:** [suíte + resultado, por exemplo, make teste verde; se não souber, (preencher)]

**Impacto:** [telas/API/migração ou só teste, pode ter várias linhas]

**Rollback:** [como voltar, pode ter várias linhas]

**Fora de escopo:** [o que vai em MR própria ou (nada)]

**Refs:** [tt-XXXX + link]
```

## Preview modo escadinha (exibir ao usuário, um bloco por MR, ordem 1→N)

```
Push (ordem 1→N): git push -u origin [branch-1], [branch-2], ...

MR 1/N
Projeto | Origem → Destino | Assignee | Reviewer
[namespace/repo] | [origem-1] → [destino-1] | [nome | Sem responsável] | [nome | Sem reviewer]
Título: [tipo/contexto-curto-tt-XXXX]
Descrição:
[body literal do(s) commit(s) desta MR, sem reescrever; se não houver body, omitir este trecho]

**Dor:** [detalhado, pode ter várias linhas]

**Repro:** [passo 1 + passo 2 + erro, pode ter várias linhas]

**Por que assim:** [por que essa solução e não outra, pode ter várias linhas]

**O que:** [detalhado, pode ter várias linhas]

**Como testar:** [comando + clique, pode ter várias linhas]

**Prova:** [suíte + resultado, por exemplo, make teste verde; se não souber, (preencher)]

**Impacto:** [telas/API/migração ou só teste, pode ter várias linhas]

**Rollback:** [como voltar, pode ter várias linhas]

**Fora de escopo:** [o que vai em MR própria ou (nada)]

**Refs:** [tt-XXXX + link]

MR 2/N
Projeto | Origem → Destino | Assignee | Reviewer
[namespace/repo] | [origem-2] → [destino-2] | [nome | Sem responsável] | [nome | Sem reviewer]
Título: [tipo/contexto-curto-tt-XXXX]
Descrição:
[body literal do(s) commit(s) desta MR, sem reescrever; se não houver body, omitir este trecho]

**Dor:** [detalhado, pode ter várias linhas]

**Repro:** [passo 1 + passo 2 + erro, pode ter várias linhas]

**Por que assim:** [por que essa solução e não outra, pode ter várias linhas]

**O que:** [detalhado, pode ter várias linhas]

**Como testar:** [comando + clique, pode ter várias linhas]

**Prova:** [suíte + resultado, por exemplo, make teste verde; se não souber, (preencher)]

**Impacto:** [telas/API/migração ou só teste, pode ter várias linhas]

**Rollback:** [como voltar, pode ter várias linhas]

**Fora de escopo:** [o que vai em MR própria ou (nada)]

**Refs:** [tt-XXXX + link]
...
```

---

**Regras:**
- O body do(s) commit(s) vem primeiro na descrição e deve ser copiado literalmente, sem reescrita; se não houver body, omitir esse trecho.
- Nunca repetir o título na descrição.
- Nunca inventar `Prova` ou `Como testar`; se faltarem dados, usar `(preencher)`.
- Escrever em PT-BR simples, usando Markdown do GitLab e sem emoji.
- Pular uma linha em branco entre cada tópico (Dor, Repro, Por que assim, etc.) para ficar detalhado mas legível, bem separadinho.
- Se o usuário pedir edição (opção `[E]` na confirmação), atualizar só o campo pedido e reexibir o template inteiro antes de confirmar outra vez.
