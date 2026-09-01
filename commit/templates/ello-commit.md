Template da mensagem de commit da **variante Ello** (projetos do Ello ERP) — usada no lugar do template conventional. Ver `../referencias/ello.md` para as regras detalhadas e `../referencias/fluxo.md` (Passo 0) para a detecção.

```
[Subject: verbo no presente, ≤80 caracteres, contexto da tela/módulo] [(tt-XXXX) | *]

[Body opcional — contexto e solução em linguagem simples; jargão técnico
só quando facilitar o entendimento; conciso, até ~400 caracteres, linhas
≤~72 chars]

[Sem rodapé — nunca Co-Authored-By nem atribuição de IA]
```

---

## Exemplos do projeto

**Bugfix com ticket (entra no changelog, sem `*`):**
```
Corrige comissão negativa de devolução não gravada (TT-3561)

Ao gravar a devolução, o cálculo de `GravaComissaoNegativa` usava a
própria consulta interna como referência, então o resultado sempre
dava zero e a comissão negativa nunca chegava à tabela de liberação.
A consulta do pedido (QueryPedido) agora é nomeada e usada
explicitamente, corrigindo o cálculo.
```

**Refatoração interna (não entra no changelog, com `*`):**
```
Extrai a configuração da distribuição de DF-e para classe própria *

A configuração do ACBr (versão do layout, pastas de XML, ambiente e
separação por ano/mês) sai de dentro de `TDistribuicaoDFe` e fica
centralizada em `TDFeConfiguracoesDistribuicao`. O comportamento
externo não muda.
```

**Feature que deve sair no changelog (sem ticket, sem `*`):**
```
Melhora a performance da listagem de produtos

O `CalcFields` executava uma consulta extra (DadosAtualizadosProduto)
para cada linha visível do grid a cada redesenho da tela, deixando o
scroll lento. Os dados agora vêm prontos na consulta principal via
join, eliminando as consultas extras durante a rolagem.
```

**Mudança de infra/tools (não entra no changelog, com `*`):**
```
Remove código morto *
```