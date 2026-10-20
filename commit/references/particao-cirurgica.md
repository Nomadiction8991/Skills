# Referência — Partição Cirúrgica de Mudanças

Orientações para dividir alterações lógicas misturadas nos mesmos arquivos ou diretórios antes de commitar em branches empilhadas.

## Régua estrita (vale primeiro, sem exceção)

- **Teste verde NUNCA decide lugar de linha.** O lugar de cada linha se decide por dono da rota, direção da dependência e camada. Teste só atesta "não quebrou" — nunca "está no lugar certo".
- **Toda linha citando rota ou recurso de outra frente é violação**, mesmo com teste verde (ex.: condição na base citando caminho que só nasce na MR2).
- **Nada de carona silenciosa.** Linha de outra frente dentro do commit, sem nota declarada, reprova o commit.
- **Tudo que é interligado permanece interligado.** Dependência se resolve empilhando e mergeando junto — NUNCA espalhando o vínculo entre branches nem fatiando a ligação.

## O que fazer

1. **Quando dividir (staging parcial com `git add -p`):**
   - Hunks ou blocos independentes dentro do mesmo arquivo.
   - Linhas de registro, rotas ou configurações desacopladas de donos distintos.
   - Teste automatizado: **o teste segue o código que testa**, nunca a pasta onde reside (se testa o painel, vai na branch do painel).

2. **Quando manter inteiro (NUNCA dividir):**
   - Condição booleana compartilhada (ex.: flags combinadas em `if`).
   - Renomeação de símbolo/método e todos os seus locais de uso.
   - Model, migration ou schema consumido por mais de uma frente: **fica na branch mais baixa que o consome**.
   - Artefato gerado por compilação/build: carona na branch base **com nota declarada no corpo**.
   - Qualquer unidade cuja separação quebre compilação ou testes isolados.

## Rascunhos e segurança (sem stash obrigatório)

- **Branch local é rascunho até o push.** Criar branch (`git checkout -b <nova> <base>`) não altera a árvore de trabalho; reescrever commit (`amend`) e reempilhar (`rebase`) são livres enquanto nada foi enviado.
- **Antes de qualquer reescrita, criar ponteiros de backup:** `git branch backup/<nome> <branch>` (um por branch da pilha). Não duplica arquivos e restaura na hora (`git reset --hard backup/<nome>`) se o ajuste sair errado.
- **Stash múltiplo é opcional e opaco:** o git permite vários nomeados (`stash push -m`, `list`, `apply`), mas stash não se revisa antes de aplicar — para ajuste fino com conferência, branch é preferível.
- **Worktree (uma pasta por branch):** serve para comparar código lado a lado; só usar para rodar suíte se o ambiente de teste enxergar o caminho da worktree.
- **Conferência de soma:** ao final da pilha, a soma dos commits deve igualar os pendentes originais (`git status` limpo no topo, nenhuma linha a mais ou a menos).
- **Nada vai ao remoto sem pedido explícito.**

## Merge da pilha: nada de órfão

- **Definição:** código órfão de pilha é fundação mergeada sem o consumidor (ex.: tabela, endpoint e model entram, a tela é rejeitada — viram peso morto para trás).
- **Regra dura:** pilha interligada mergeia junta, de baixo para cima. NUNCA aterrissar a base sozinha sem decisão explícita do usuário.
- **Se o topo for rejeitado**, decidir na hora o destino da base: reverter junto, ou manter órfã **declarada** (dono + motivo + prazo) — órfão silencioso reprova.
- **Independência técnica NÃO autoriza merge parcial.** Cada branch precisa se sustentar sozinha (compilar + testar) *e* só faz sentido mergeada junto com a pilha. São duas fases com duas réguas.

## Como verificar

- `git diff --cached --stat` antes de cada commit: só a fatia da unidade deve estar em staging (procurar intruso de outra frente pelo nome do arquivo; em arquivo compartilhado, conferir hunk a hunk).
- Rodar a suíte na branch após cada commit — vale como "não quebrou", nunca como prova de lugar certo.
- Se a base quebrar por ausência de arquivo alocado em frente posterior, **mover o arquivo para a base** (não duplicar código).
- Antes do push: para cada branch da pilha, listar quem a consome (quem chama essa rota? quem lê essa tabela? quem usa esse model?). Sem consumidor na pilha = órfão — parar e decidir.

## Portão verde por branch (rígido, sem exceção)

- **Teste viaja com o código e cada branch SÓ anda verde.** O teste segue o código que testa; ao commitar a branch, rodar a suíte nela antes de empilhar a próxima.
- **Branch quebrada não anda.** Se falhar: parar na hora, mostrar a falha, corrigir na própria branch, repetir até verde. NUNCA empilhar sobre base vermelha, NUNCA "corrigir depois".
- **Verde de uma não cobre a outra.** Cada branch roda sua suíte isolada; o verde da base não aprova o topo.
- **Volta final obrigatória:** depois da última branch, revisitar cada uma (checkout + suíte + soma dos diffs). Só declara pronto com todas verdes e a soma igual aos pendentes originais.

## Quando reprovar

- Reprovar staging de arquivo compartilhado inteiro quando contém hunks de outra branch.
- Reprovar divisão de linha única de lógica (ex.: quebrar expressão condicional em dois commits).
- Reprovar teste alocado na branch errada só por convenção de diretório.
- Reprovar exceção silenciosa (código de outra frente sem nota declarada).
- Reprovar merge parcial de pilha interligada sem decisão explícita.
- Reprovar avanço sobre branch vermelha, "corrigir depois" ou entrega sem a volta final em todas as branches.
