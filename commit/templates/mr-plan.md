Template da apresentação final do plano de separação (Passo 2.5 de `../referencias/fluxo.md`) — só preencher os `[colchetes]`, sem alterar a estrutura.

Regras do grafo:
- Desenhar a **topologia real**: branches independentes saem lado a lado da base; branch dependente sai aninhada da branch pai, nunca da base.
- Usar `├──` para irmãs e `└──` para a última filha de cada nível; aninhar a continuação com `│   `.
- Cada linha do grafo tem **só o nome da branch**. Nada de commit no grafo.
- Abaixo do grafo, **uma seção por branch**: cabeçalho com o nome entre linhas de `===` (uma antes, uma depois), seguido de subject, body e justificativa (1–2 frases: por que existe e por que parte desta base).
- **Sem lista de arquivos** no grafo e nas seções. A confirmação do Passo 5 continua exibindo mensagem + arquivos (regra geral #9).
- Ideal **1 branch = 1 MR = 1 commit**.
- Nome da branch: `tipo/resumo-do-que-foi-feito-tt-XXXX` — sempre com prefixo (`feat`, `fix`, `chore`, etc.), resumo o mais completo possível, `tt-XXXX` no final só quando houver ticket. Máximo 70 caracteres no total; se passar, encurtar o resumo em palavras inteiras, nunca o prefixo nem o ticket. Sem parênteses ou espaços (colam em qualquer comando git).

```
[base, ex.: main]
 ├── [nome-branch-1]
 │    └── [nome-branch-2]
 └── [nome-branch-3]
```

================================================================
[nome-branch-1]
================================================================
`[tipo(escopo): subject do commit 1]`

[body — o quê mudou e por quê, conciso]

Por que existe / base: [1–2 frases]

================================================================
[nome-branch-2]
================================================================
`[tipo(escopo): subject do commit 2]`

[body — o quê mudou e por quê, conciso]

Por que existe / base: [1–2 frases]

================================================================
[nome-branch-3]
================================================================
`[tipo(escopo): subject do commit 3]`

[body — o quê mudou e por quê, conciso]

Por que existe / base: [1–2 frases]

**Exemplo preenchido (projeto Ello: duas saindo da main + uma empilhada):**
```
main
 ├── feat/adiciona-ciencia-diferenciada-para-contador-tt-3790
 │    └── fix/nao-grava-ciencia-sem-resumo-tt-3790
 └── chore/ajusta-log-da-manifestacao-tt-3801
```

================================================================
feat/adiciona-ciencia-diferenciada-para-contador-tt-3790
================================================================
`Adiciona ciência diferenciada para contador na MD-e (tt-3790)`

Ciência e confirmação passam a ser diferenciadas para o contador na MD-e, que antes seguia a regra geral.

Por que existe / base: é a base da correção abaixo e não depende de nada, então parte da main.

================================================================
fix/nao-grava-ciencia-sem-resumo-tt-3790
================================================================
`Não grava ciência sem resumo na MD-e (tt-3790)`

Sem resumo retornado, a ciência era gravada vazia; agora só grava com resumo.

Por que existe / base: só faz sentido sobre a regra nova, então empilha sobre a branch feat.

================================================================
chore/ajusta-log-da-manifestacao-tt-3801
================================================================
`Ajusta log da manifestação para modo contador (tt-3801)`

O log passa a registrar o modo contador para facilitar o suporte.

Por que existe / base: independente das outras duas, parte da main.
