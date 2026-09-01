# Referência — Criar Merge Request

Passo a passo para abrir uma MR no GitLab.

---

## Princípio central: título e descrição vêm dos commits, não são escritos do zero

Diferente de outras skills que constroem título/corpo manualmente, aqui o título e a descrição da MR **geralmente já existem** — vêm dos commits da branch, que já estão organizados (conventional commits). A skill não deve inventar ou reescrever esse conteúdo; só **montar a partir do que já existe** e exibir para o usuário revisar.

Só perguntar/editar manualmente título ou descrição se o usuário pedir explicitamente uma mudança na prévia (Passo 5/6) — nunca perguntar "qual título você quer?" como primeira ação.

---

## Passo 1 — Branch de origem e destino

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

## Passo 2 — Coletar os commits da branch

```bash
git log <destino>..<origem> --pretty=format:"%s%n%b" --reverse
```

Esses commits são a fonte da verdade do título e da descrição — não reescrever o conteúdo técnico deles, só organizar.

---

## Passo 3 — Montar título

- **Um único commit na branch:** usar a subject line do commit como título, sem alteração.
- **Múltiplos commits:** usar o `type(scope)` predominante entre os commits (conventional commit) + um resumo curto do conjunto. Se os commits não convergirem para um resumo óbvio, usar o nome da branch (convertido para texto legível) como base do título.

---

## Passo 4 — Montar descrição

Listar os commits em ordem cronológica, um por linha, reaproveitando a subject line de cada um (não resumir/reescrever o conteúdo técnico — os commits já estão organizados):

```
- feat(x): ...
- fix(y): ...
```

Se algum commit tiver corpo (`%b`) relevante além da subject line, incluir como sub-item.

---

## Passo 5 — Responsável (assignee) *(sempre perguntar)*

> "Quem será o responsável pela MR? `[1] Eu mesmo` `[2] Outra pessoa` `[3] Sem responsável`"

- **1:** usar o usuário autenticado no GitLab (token) como assignee.
- **2:** perguntar o nome/usuário GitLab da pessoa.
- **3:** não preencher `assignee_id`.

---

## Passo 6 — Exibir template (preview obrigatório)

Ler `../templates/mr.md`, preencher os `[colchetes]` com os dados dos Passos 1–5 e exibir ao usuário exatamente assim, fora de bloco de código.

**A MR NÃO deve ser criada neste momento.**

---

## Passo 7 — Confirmar

> "Os dados estão corretos? Deseja criar a MR?"
> `[S] Sim, criar` | `[N] Cancelar` | `[E] Editar campo`

- **S:** seguir para o Passo 8.
- **N:** cancelar sem criar nada.
- **E:** perguntar qual campo mudar (título, descrição, destino, responsável), coletar novo valor, voltar ao Passo 6.

(Ver regra de prévia/confirmação em `regras-gerais.md` — vale integralmente aqui.)

---

## Passo 8 — Criar via MCP

```
mcp__gitlab__create_merge_request(
  project_id     = "<namespace/repo, a partir do remote origin>",
  source_branch  = "<origem>",
  target_branch  = "<destino>",
  title          = "<montado no Passo 3>",
  description    = "<montado no Passo 4>",
  assignee_id    = "<id, se atribuído no Passo 5>"
)
```

Para descobrir `project_id`, ler o remote:
```bash
git remote get-url origin
```

Após a criação, exibir ao usuário o link/IID da MR retornado.
