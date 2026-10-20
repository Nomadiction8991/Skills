# Referência — Fluxo do Commit

Passo a passo completo para criar um commit bem formatado.

Ver `regras-gerais.md` — vale integralmente para a mensagem montada neste fluxo.

---

## Passo 0 — Detectar projeto Ello

O **nome/caminho do repositório manda**: `git rev-parse --show-toplevel` → se `ello` aparecer como segmento próprio do caminho (delimitado por `/`, `-`, `_` ou fim — vale `.../ello/...`, `ello-app`, `app-ello`; **não** vale substring como `hello`), **é projeto Ello, sem contestação**. Qualificativos em `AGENTS.md`/`README.md` ("satélite", "backup", etc.) não desqualificam — continuam sendo Ello.

Só quando o nome/caminho não disser nada, leia `AGENTS.md` e `README.md` da raiz do projeto → se citarem "Ello" (ex.: "Ello ERP"), é projeto Ello.

**Se for Ello:** monte a mensagem com `ello.md` e `../templates/ello-commit.md` — a variante substitui o subject, o corpo e o rodapé TomTicket. No Passo 4, pule a parte Conventional Commits e use a variante Ello. As demais regras gerais (limites de tamanho, rodapé de IA, pre-commit) continuam valendo.

---

## Passo 1 — Verificar Staging e Branch

Checar `git status` (já injetado no SKILL.md, seção "Estado Atual do Repositório").

- Se **houver** arquivos em staging: commitar **apenas** esses arquivos — não adicionar mais nada.
- Se **não houver** nenhum arquivo em staging: não adicione ainda — o Passo 2 decide: commit único → `git add` geral; separação (2.5) → staging cirúrgico na execução do plano.

**Branch atual** (ver `git branch --show-current` no SKILL.md):

- **Projeto Ello:** nunca commitar direto na base (`main`/`master`). Se estiver nela, criar a branch antes (`git checkout -b <nome>`) — vale o padrão de nome de `../templates/mr-plan.md`.
- **Outros projetos:** seguir a convenção do projeto (`AGENTS.md`/`README.md`) ou o que o usuário pedir.

---

## Passo 1.5 — Verificar amend (1 commit por branch)

Se a branch **já tem commit** (HEAD não é o commit inicial da branch) e há novas alterações:

1. Verificar se o último commit **já foi enviado ao remoto**: primeiro confira se a branch tem remota (`git branch -r | grep <branch>`); sem remota → ainda não enviado. Com remota, `git log --oneline origin/<branch>..HEAD` vazio → não enviado; com commits → enviado.
2. Verificar se as alterações atuais são do **mesmo trabalho** do commit existente (mesmo contexto/lógica). Se forem outro trabalho → **ir ao Passo 2.5 antes de decidir**: pode virar branch empilhada em vez de segundo commit na mesma branch.
3. **Não enviado + mesmo trabalho** → informar o usuário que será um amend, fazer `git add` dos arquivos e `git commit --amend`. Reavaliar o diff completo (commit anterior + novos) e **editar a mensagem se necessário** para descrever o conjunto final; se a mensagem atual já cobre, mantê-la.
4. **Já enviado** (e mesmo trabalho) → não usar amend (regra geral #8): criar commit novo e avisar.

Em seguida, seguir para o Passo 2 com o diff resultante.

---

## Passo 2 — Analisar o Diff

Executar `git diff --cached` (ou `git diff` se acabou de fazer `git add`) para entender exatamente o que está sendo commitado — nunca escrever a mensagem só pelo `git status`/nomes de arquivo.

Analisar se há **múltiplas alterações lógicas distintas** no diff (ex.: uma refatoração + uma feature nova, ou mudanças em dois módulos não relacionados).

- Se detectar múltiplas alterações distintas, **ir ao Passo 2.5 antes de decidir**: primeiro avalia-se a separação em MRs/branches; só o que ficar junto no mesmo MR pode ser dividido em commits menores. Perguntar ao usuário se prefere separar ou manter tudo junto.
- Se for uma alteração lógica única (mesmo que toque vários arquivos), seguir com um commit só (o Passo 2.5 confirma antes de montar a mensagem).

---

## Passo 2.5 — Avaliar separação em MRs (antes de montar a mensagem)

Todo commit desta skill **vai para MR** (ideal **1 branch = 1 MR = 1 commit**) — exceto em projeto que por convenção commita direto na base (repo pessoal/docs sem MR flow): aí o 2.5 avalia só "1 commit por unidade lógica", sem criar branches. Antes de montar qualquer mensagem, avaliar o que faz sentido manter junto no mesmo MR e o que dá para separar.

**Diff grande** (medir com `git diff --numstat`: mais de ~500 linhas ou ~10 arquivos): recomendar a divisão com ênfase e avisar a carga do revisor — MR grande mistura riscos e o rollback vira tudo-ou-nada.

Fonte principal: **alterações pendentes** (`git status --porcelain`, `git diff` + `git diff --cached`) — na maioria das vezes é aqui que está o trabalho a separar. Histórico da branch (`git log <base>..HEAD`, com `<base>` = `main`/`master`/`develop`, descoberta via `git branch -a`) entra só como contexto secundário.

> **Regra:** lance um sub-agente isolado de propósito geral (o tipo disponível no ambiente) em contexto isolado só para esta avaliação — o prompt inclui os pendentes reais, ticket/chamado já conhecido (se houver), `regras-gerais.md` (regras #8 e #10), `../templates/mr-plan.md` (padrão de nome e grafo) e este Passo 2.5, com a instrução de nunca usar o histórico da conversa, só o git real. O sub-agente **nunca** executa `git checkout`, `git branch` ou `git commit`; ele só devolve o plano de separação para o agente principal.

O sub-agente agrupa os pendentes em **unidades lógicas** (por área/módulo, por natureza `feat` vs `fix` vs `refactor` vs `chore`, por dependência: A funciona sem B?) e devolve:

1. O que **mantém junto** (mesmo MR) e por quê.
2. O que **separa** — plano empilhado: ordem da base ao topo, o que vai em cada branch, qual é a base de cada uma, com dependência sempre **unidirecional** (base → branch1 → branch2).
3. O que **não separa bem** e por quê (dependência circular, mudança atômica como rename + todos os usos, ou unidade que sozinha quebra o build/testes).

Arquivos compartilhados entre frentes **não vetam** a separação: propor o split em camadas (schema/migrations → coleta/ingestão → backend → UI/painel; `chore`/`refactor` sempre à parte) e aplicar partição cirúrgica no que exigir corte dentro do arquivo (`particao-cirurgica.md` — staging parcial, teste segue o código, régua estrita: teste verde NUNCA decide lugar de linha). **Refactor com mudança de comportamento nunca vai de carona na feature** — MR própria, com recomendação forte.

O agente principal primeiro confere se todo pendente entrou em exatamente uma unidade (nada de fora, nada em duas) e apresenta o plano no formato de `../templates/mr-plan.md` — grafo só com os nomes (topologia real: ramos lado a lado ou aninhados) e uma seção por branch com subject, body e justificativa, sem listar arquivos (o padrão vive no template) — e pergunta se o usuário quer separar agora ou manter tudo junto. Com mais de 3 branches na pilha, alertar que a revisão fica pesada e sugerir fundir as menores unidades. **Nunca** criar branches sem confirmação explícita. Se nem em camadas separar bem, diz o porquê em 1–2 frases e segue.

Com `[S]` para separar, o agente principal executa o plano antes de montar qualquer mensagem: cria as branches na ordem da base ao topo (`git checkout -b <nova> <base>` a partir da base certa de cada uma — sem stash, a árvore viaja intacta), leva para cada branch só a fatia da sua unidade (`git add` cirúrgico + `git diff --cached --stat` antes de cada commit) e roda os Passos 1–5 em cada branch (1 commit por branch), sem reabrir o 2.5 — a separação já foi decidida — e pulando o Passo 1.5 (branch recém-criada não tem commit próprio para amend). Antes de criar cada branch, conferir `git branch --list <nome>` — se já existir, parar e informar o que já foi criado e como retomar. Antes de qualquer reescrita (`amend`/`rebase`), criar ponteiros de backup (`git branch backup/<nome> <branch>`) e apagá-los ao final da pilha verde (`git branch -d backup/<nome>`). Depois de commitar em cada branch da pilha, rodar nela a suíte de testes do projeto (descobrir o comando antes de começar — `Makefile`, `package.json`, `composer.json`, etc.) como atestado de "não quebrou" (nunca como prova de lugar certo); se falhar, parar, mostrar a falha e não avançar para a próxima branch. Se o projeto não tiver suíte de testes, declarar "sem testes a rodar" e seguir — só parar em falha real de teste executado. Ao final, conferir a soma: os diffs commitados na pilha devem igualar os pendentes originais (nenhuma linha a mais ou a menos). Antes do push, aplicar a regra de nada-de-órfão (`particao-cirurgica.md`): pilha interligada mergeia junta, de baixo para cima — NUNCA aterrissar a base sozinha sem decisão explícita. No fim da pilha, fazer a volta final: checkout em cada branch + suíte verde + soma igual aos pendentes — só então declara pronto.

Só depois dessa decisão seguir para o Passo 3/4 (montar a mensagem do trabalho atual).

---

## Passo 3 — Verificar Chamado Vinculado (só em projeto Ello)

Aplicar `regras-gerais.md` (regra #5): **somente** em projeto Ello (Passo 0) — se a skill `chamado-tomticket` estiver disponível, perguntar se este commit está relacionado a um chamado do TomTicket antes de montar a mensagem. Em projetos não-Ello, **pular** esta verificação.

---

## Passo 3.5 — Changelog no deploy (só em projeto Ello com changelog)

Em projeto Ello (Passo 0), **primeiro verifique se o projeto tem changelog**: procurar `CHANGELOG*` na raiz do repositório (`git rev-parse --show-toplevel`) ou menção a "changelog" no `AGENTS.md`/`README.md` da raiz.

**Sem changelog → pular esta pergunta** e seguir sem ` *` (o marcador só serve para excluir o commit de um changelog existente). Com changelog, **pergunte ao usuário** se o commit sai ou não no changelog — nunca assuma. A lógica ticket/` *` vive em `ello.md` (§2, fonte única); aqui só o procedimento da pergunta:

- Pergunte **sempre que houver changelog**, mesmo com ticket — confirme com o usuário que a decisão está correta (o `(tt-XXXX)` já implica "sai no changelog"; se o usuário disser que não deve sair, só comente a exceção).
- Formule a pergunta com as opções claras: "sai no changelog" / "não sai no changelog" (e "tem ticket" quando aplicável), com a recomendada.
- Se não for possível perguntar (ambiente não interativo), assuma "deve sair no changelog" (sem ` *`).

---

## Passo 4 — Montar a Mensagem

> **Regra:** lance um sub-agente isolado de propósito geral (o tipo disponível no ambiente) em contexto isolado para montar a mensagem — o prompt inclui o diff real, `regras-gerais.md` e os Passos 0 a 3.5 deste fluxo, **exceto o Passo 2.5** (a separação em MRs já foi decidida antes e **não** se reavalia aqui). O sub-agente **nunca** executa `git commit`; ele só devolve a mensagem montada (e o aviso de amend, se aplicável) para o agente principal.

**Se for projeto Ello (Passo 0):** usar `../templates/ello-commit.md` com as regras de `../references/ello.md`.

**Senão:** usar o template de `../templates/commit.md`, preenchendo com base no diff real (Passo 2) — nunca a partir do pedido do usuário isoladamente, nem só dos nomes dos arquivos alterados. Seguir a especificação Conventional Commits (`conventional-commits.md`) para tipo, escopo e formato, e `regras-gerais.md` para corpo/linguagem/atribuição/chamado vinculado.

---

## Passo 5 — Criar o Commit (com confirmação)

Para cada commit (ou o único, se não dividido no Passo 2.5), **antes de executar qualquer `git commit`** (regra geral #9):

1. Exibir a prévia: **mensagem final pronta** (subject + body) e a **lista de arquivos** que entrarão no commit.
2. Se for `--amend` (Passo 1.5), informar isso e qual commit será alterado.
3. Pedir confirmação explícita `[S] Sim` / `[N] Não` e aguardar a resposta.
4. Só com `[S]`, executar `git commit` (ou `git commit --amend`) com a mensagem montada.

Ver `regras-gerais.md` (regras #3, #4 e #9). Ao final, revisar o diff do commit para garantir que a mensagem corresponde exatamente às alterações. Em seguida, sugerir `/revisao` antes do push/MR.
