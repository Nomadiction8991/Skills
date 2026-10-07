# Referência — Criar Merge Request

Passo a passo para abrir uma MR no GitLab.

---

## Princípio central: título e descrição vêm dos commits, não são escritos do zero

Diferente de outras skills que constroem título/corpo manualmente, aqui o título e a descrição da MR **geralmente já existem** — vêm dos commits da branch, que já estão organizados pela skill de commit (ideal 1 branch = 1 MR = 1 commit). A skill não deve inventar ou reescrever esse conteúdo; só **montar a partir do que já existe** e exibir para o usuário revisar.

Regra de mapeamento (vale para modo único e escadinha):

- **1 commit exclusivo na branch:** **Título da MR = subject do commit**, sem alteração; **Descrição da MR = body do commit**, sem alteração. Se o commit não tem body, a descrição fica vazia.
- **2+ commits exclusivos na branch:** título e descrição seguem a síntese dos Passos 3–4 do modo único.
- Em ambos os casos: **nunca repetir o título na descrição** e não resumir/reescrever o conteúdo técnico — os commits já estão organizados.

Só perguntar/editar manualmente título ou descrição se o usuário pedir explicitamente uma mudança na prévia — nunca perguntar "qual título você quer?" como primeira ação.

---

## Normalização do remote (obrigatório antes de MCP/API, vale para os dois modos)

Obter a URL (`git remote get-url origin`) e extrair tudo após o host como path (`grupo/sub/repo`), removendo o sufixo `.git` e descartando credencial embutida (`user:pass@`) — vale para qualquer host GitLab (oficial ou self-hosted, com ou sem porta), nos formatos: scp-like `git@HOST:grupo/sub/repo.git`, `ssh://git@HOST[:porta]/grupo/sub/repo.git`, `http://HOST/grupo/sub/repo.git`, `https://HOST[:porta]/grupo/sub/repo.git` e `https://user:token@HOST/grupo/sub/repo.git` (ex.: `git@gitlab.exemplo.com:grupo/frontend/app.git` → `grupo/frontend/app`). URL-encodar cada `/` como `%2F` SOMENTE nas chamadas REST diretas de endereço (`GET /projects/<namespace%2Frepo>`); no MCP (`project_id`), usar o nome puro (`namespace/repo`) ou o id numérico — nunca a URL bruta.

---

## Modo escadinha (stack) — publicar branches + abrir uma MR por branch

Usar quando o pedido mencionar publicar branches, escadinha, stack, pilha, "uma MR por branch", ou quando houver 2+ branches locais não publicadas empilhadas (cenário padrão pós-skill de commit). Se houver só 1 branch, seguir o modo único abaixo.

### Passo 0 — Validar MCP (somente leitura, sem poluir o repo)

1. `claude mcp list` → esperar `gitlab: ... - ✔ Connected`. Se não existir, seguir `install.md` antes de continuar.
2. `claude mcp get gitlab` → conferir escopo e `GITLAB_API_URL`, sem exibir o token.
3. Teste funcional read-only com a mesma credencial do MCP (não cria/comenta nada):
   - `GET /user` → 200 (username autenticado).
   - `GET /projects/<namespace%2Frepo>` → 200 (id, path_with_namespace).
   - `GET /projects/<id>/merge_requests?state=opened` → 200 (lista de MRs abertas, para não duplicar).
4. Exceção — somente se as tools `mcp__gitlab__*` estiverem indisponíveis/deferred nesta sessão (ToolSearch não carrega): usar fallback via API direta com o token já configurado em `~/.claude.json` (`mcpServers.gitlab.env`). Obter e usar o valor em processo único, sem `cat` e sem imprimir o token (ex.: script Python/`urllib` compacto que lê o JSON, extrai o token em memória e já faz a chamada com header `PRIVATE-TOKEN`); nunca exibir o token na resposta nem gravá-lo em arquivo da skill. Todas as chamadas usam como base o `GITLAB_API_URL` conferido no item 2. Endpoints mínimos: `GET /user` (id/username; `assignee_id` numérico quando necessário); `GET /projects/<namespace%2Frepo>` (id, path_with_namespace — path normalizado conforme `Normalização do remote`); `GET /projects/<id>/merge_requests?state=opened` (não duplicar); `POST /projects/<id>/merge_requests` com `source_branch`, `target_branch`, `title` e `description`, mais `assignee_id` só quando houver responsável (opções 1–2, id numérico); na opção 3 (Sem responsável), omitir o campo. A opção "Outra pessoa" exige resolver o nome via `GET /users?username=<nome>` para obter o id numérico antes do POST; nunca enviar username como `assignee_id`.

### Passo 1 — Detectar a escadinha (somente leitura)

Rodar nesta ordem e guardar o resultado:

```bash
git branch -vv
git status -sb
git log --oneline --graph --decorate -30
git for-each-ref --format='%(refname:short)|%(upstream:short)|%(upstream:trackshort)|%(objectname:short)|%(committerdate:short) %(subject)' refs/heads/
git branch -r | head -40
```

Descobrir o pai de cada branch pelo grafo (`git log --oneline --graph --decorate`): a branch filha contém os commits da pai + commits próprios (ideal: 1 próprio).

Normalizar o remote conforme a seção `Normalização do remote` acima (obrigatório antes de MCP/API).

Validar o pai antes de montar as MRs: cada `"<pai>..<branch>"` deve trazer os commits exclusivos esperados (ao menos 1; ideal 1), o pai deve ser ancestral da branch (`git merge-base --is-ancestor "<pai>" "<branch>"`) e os subjects do range devem ser os esperados da branch. Se falhar, se o range vier vazio/inesperado/ambíguo, se trouxer merges/commits de outra linha, ou se trouxer commits demais para uma entrega (bem acima do ideal de 1 por branch, ex. a pilha inteira — indica nível pulado), parar e pedir confirmação explícita do pai e da cadeia ao usuário antes de continuar.

Para cada branch local da stack, mostrar os commits exclusivos vs a branch pai + diff stat:

```bash
git log --oneline "<pai>..<branch>"
git diff --stat "<pai>...<branch>" | tail -15
```

E coletar subject/body exatos de cada uma:

```bash
git log --reverse --pretty=format:"SUBJECT:%s%n---BODY---%n%b%n---END---" "<pai>..<branch>"
```

(Com 1 commit exclusivo, o resultado traz o subject/body daquele commit; com 2+, traz todos para a síntese dos Passos 3–4 do modo único.)

Confirmar que não há MRs abertas duplicando a stack (Passo 0, item 3).

### Passo 2 — Montar a stack ordenada 1→N (espelhar a dependência das branches)

Olhar como as branches estão empilhadas e replicar nas MRs: se uma branch depende da outra, a MR também depende.

- Usar os pais descobertos no Passo 1 (grafo + commits exclusivos vs pai).
- Regra: `target` da MR = branch pai. Só a primeira da stack mira a branch padrão do remoto (detectar como no Passo 1 do modo único: `git remote show origin | grep "HEAD branch"`).
- Exemplo: `feat/base → <padrão>`, `feat/meio → feat/base`, `chore/topo → feat/meio`.
- Cada item: origem, destino (= pai), título (subject com 1 commit exclusivo, síntese do Passo 3 do modo único com 2+), descrição (body com 1 commit exclusivo, síntese do Passo 4 do modo único com 2+).

### Passo 3 — Responsável (assignee) *(sempre perguntar, uma vez para a stack toda)*

> "Quem será o responsável pelas MRs? `[1] Eu mesmo` `[2] Outra pessoa` `[3] Sem responsável`"

- **1:** usar o usuário autenticado (`GET /user`) como assignee.
- **2:** perguntar o nome/usuário GitLab da pessoa.
- **3:** não preencher `assignee_id`.

### Passo 4 — Exibir prévia da stack (obrigatório, antes de qualquer push/MR)

Ler `../templates/mr.md` (formato stack), preencher um bloco por MR (projeto, origem → destino, responsável, título e descrição conforme o princípio central — 1 commit exclusivo — ou a síntese dos Passos 3–4 do modo único — 2+ commits) e exibir ao usuário exatamente assim, fora de bloco de código.

**Nada é publicado ou criado neste momento** (ver `regras-gerais.md`).

### Passo 5 — Confirmar a stack

> "Posso publicar as branches (push -u origin, ordem 1→N) e abrir as N MRs encadeadas?"
> `[S] Sim, enviar` | `[N] Cancelar` | `[E] Editar campo`

- **S:** seguir para o Passo 6.
- **N:** cancelar sem push e sem criar nada.
- **E:** perguntar qual MR/campo mudar (título, descrição, destino, responsável), coletar novo valor, voltar ao Passo 4.

### Passo 6 — Publicar as branches (ordem 1→N)

```bash
git push -u origin "<branch-1>"
git push -u origin "<branch-2>"
# ... uma por vez, da base ao topo
```

Só avançar para o Passo 7 se todos os pushs funcionarem. Se um falhar, parar e mostrar o erro.

### Passo 7 — Criar as MRs encadeadas + verificar

Via MCP (`mcp__gitlab__create_merge_request` com `project_id` normalizado conforme `Normalização do remote` — `namespace/repo` ou id do `GET /projects/...`, nunca a URL bruta do remote — `source_branch`, `target_branch`, `title`/`description` conforme o princípio central — 1 commit exclusivo — ou a síntese dos Passos 3–4 do modo único — 2+ commits, `assignee_id` se houver) ou via fallback API direta da exceção do Passo 0 (item 4) quando deferred. Uma MR por branch, na ordem 1→N.

Após criar, verificar e exibir: lista de MRs abertas (`!<iid> <origem> -> <destino> | <título>` + `web_url`) e `git branch -vv` com upstream tracking.

---

## Modo único (1 branch → 1 MR)

### Passo 1 — Branch de origem e destino

1. **Origem:** a branch atual do repositório.
   ```bash
   git branch --show-current
   ```
2. **Destino:** detectar a branch padrão do remoto e sugerir como `[1]`:
   ```bash
   git remote show origin | grep "HEAD branch"
   ```
   Apresentar: `"Branch de destino? [1] <padrão detectado> (recomendado) [2] Outra"` — se `[2]`, perguntar o nome.

---

### Passo 2 — Coletar os commits da branch

```bash
git log <destino>..<origem> --pretty=format:"%s%n%b" --reverse
```

Esses commits são a fonte da verdade do título e da descrição — não reescrever o conteúdo técnico deles, só organizar.

---

### Passo 3 — Montar título

- **Um único commit na branch:** usar a subject line do commit como título, sem alteração.
- **Múltiplos commits:** usar o `type(scope)` predominante entre os commits (conventional commit) + um resumo curto do conjunto. Se os commits não convergirem para um resumo óbvio, usar o nome da branch (convertido para texto legível) como base do título.

---

### Passo 4 — Montar descrição

Título não se repete na descrição. Regra:

- **Um único commit:** descrição = body do commit (`%b`). Se não tem body, descrição vazia.
- **Múltiplos commits:** listar os commits em ordem cronológica, um por linha, reaproveitando a subject line de cada um (não resumir/reescrever o conteúdo técnico — os commits já estão organizados):

```
- feat(x): ...
- fix(y): ...
```

Se algum commit tiver corpo (`%b`) relevante além da subject line, incluir como sub-item.

---

### Passo 5 — Responsável (assignee) *(sempre perguntar)*

> "Quem será o responsável pela MR? `[1] Eu mesmo` `[2] Outra pessoa` `[3] Sem responsável`"

- **1:** usar o usuário autenticado no GitLab (token) como assignee.
- **2:** perguntar o nome/usuário GitLab da pessoa.
- **3:** não preencher `assignee_id`.

---

### Passo 6 — Exibir template (preview obrigatório)

Ler `../templates/mr.md` (formato único), preencher os `[colchetes]` com os dados dos Passos 1–5 e exibir ao usuário exatamente assim, fora de bloco de código.

**A MR NÃO deve ser criada neste momento.**

---

### Passo 7 — Confirmar

> "Os dados estão corretos? Deseja criar a MR?"
> `[S] Sim, criar` | `[N] Cancelar` | `[E] Editar campo`

- **S:** seguir para o Passo 8.
- **N:** cancelar sem criar nada.
- **E:** perguntar qual campo mudar (título, descrição, destino, responsável), coletar novo valor, voltar ao Passo 6.

(Ver regra de prévia/confirmação em `regras-gerais.md` — vale integralmente aqui.)

---

### Passo 8 — Criar via MCP

```
mcp__gitlab__create_merge_request(
  project_id     = "<nome puro `namespace/repo` ou id numérico, nunca a URL bruta>",
  source_branch  = "<origem>",
  target_branch  = "<destino>",
  title          = "<montado no Passo 3>",
  description    = "<montado no Passo 4>",
  assignee_id    = "<id, se atribuído no Passo 5>"
)
```

Para descobrir `project_id`, seguir a seção `Normalização do remote` acima; nunca usar a URL bruta do remote. Em sessão deferred, usar o fallback via API direta da exceção do Passo 0 da escadinha (item 4).

Após a criação, exibir ao usuário o link/IID da MR retornado.
