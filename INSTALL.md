# Instalação

Como instalar as skills deste repositório via **[skills.sh](https://skills.sh)** — o marketplace público de skills (instalador `npx skills` da Vercel).

> Repositório: `Nomadiction8991/Skills` → https://skills.sh/Nomadiction8991/Skills

## Requisitos

- Node.js 18+ (para `npx`)
- `git` (o CLI usa suas credenciais Git/GitHub CLI/SSH quando necessário)

## Instalação via `skills.sh` (recomendado)

O CLI `skills` é a forma oficial de instalar skills e funciona com OpenCode, Muse, Cursor, Codex e outros 70+ agentes.

### Instalar todas as skills do repo

```bash
npx skills add Nomadiction8991/Skills
```

### Instalar uma skill específica

```bash
npx skills add Nomadiction8991/Skills --skill commit
npx skills add Nomadiction8991/Skills --skill mr-gitlab
npx skills add Nomadiction8991/Skills --skill chamado-tomticket
```

### Outras variações úteis

```bash
# usar sem instalar (execução única)
npx skills use Nomadiction8991/Skills --skill commit

# listar instaladas
npx skills list
npx skills list --global

# atualizar
npx skills update Nomadiction8991/Skills

# remover
npx skills remove Nomadiction8991/Skills --skill commit

# fixar versão (tag/commit)
npx skills add Nomadiction8991/Skills@v0.1.0
```

### Onde instala

O CLI detecta o agente e instala no local correto:

- **OpenCode:** `.opencode/skills/` (projeto) ou `~/.config/opencode/skills/` (global)
- **Muse:** `~/.claude/skills/` ou `.claude/skills/`
- Método padrão é **symlink** (fonte única, fácil de atualizar) — use cópia se symlinks não forem suportados.

## Verificação

Após instalar, reinicie a sessão do agente e teste:

- `/commit` — commit no padrão Conventional Commits
- `/chamado-tomticket` — fluxos do TomTicket
- `/criar-skill` — criar/refatorar skills
- `/mr-gitlab` — na primeira execução verifica o MCP GitLab (ver `mr-gitlab/referencias/install.md`)

Confirme onde instalou:

```bash
npx skills list
ls ~/.config/opencode/skills/
ls .opencode/skills/
ls ~/.claude/skills/
```

## Instalação manual (sem `npx`)

Se preferir não usar o marketplace, copie a pasta da skill:

```bash
# OpenCode global
cp -r commit ~/.config/opencode/skills/commit

# Projeto local
cp -r commit .opencode/skills/commit
```

Cada skill é autocontida: basta a pasta com `SKILL.md` + `references/` + `templates/`.

## Skills disponíveis

| Skill | Gatilho | Pasta |
|-------|---------|-------|
| Chamado TomTicket | `/chamado-tomticket` | `chamado-tomticket/` |
| Commit | `/commit` | `commit/` |
| Criar Skill | `/criar-skill` | `criar-skill/` |
| Entreviste-me | `/entreviste-me` | `entreviste-me/` |
| Frontend Design | `/frontend-design` | `frontend-design/` |
| Linguagem | `/linguagem` | `linguagem/` |
| Manter Sessão | `/manter-sessao` | `manter-sessao/` |
| MR GitLab | `/mr-gitlab` | `mr-gitlab/` |
| Revisão | `/revisao` | `revisao/` |

## Docs

- CLI `skills`: https://www.skills.sh/docs/cli
- Pacote npm: https://www.npmjs.com/package/skills
