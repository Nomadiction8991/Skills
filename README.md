# Skills — Repositório Versionado

Repositório versionado de **skills** para agentes de IA (OpenCode / Muse).

Cada skill é uma pasta com `SKILL.md` enxuto + `references/` e `templates/` modulares.

## Skills disponíveis

| Skill | Gatilho | Descrição |
|-------|---------|-----------|
| `chamado-tomticket` | `/chamado-tomticket` | Gerencia chamados TomTicket (abrir, editar, ler, comentar, transferir, pesquisar) |
| `commit` | `/commit` | Cria commits no padrão Conventional Commits |
| `linguagem` | `/linguagem` | — |
| `manter-sessao` | `/manter-sessao` | — |
| `mr-gitlab` | `/mr-gitlab` | Gerencia Merge Requests no GitLab |
| `revisao` | `/revisao` | — |

## Estrutura

```
.
├── AGENTS.md               # regra de desambiguação: neste workspace, "skill" = skill deste repo
├── chamado-tomticket/
│   ├── SKILL.md
│   ├── referencias/
│   └── templates/
├── commit/
└── ...
```

## Instalação

Guia completo em **[INSTALL.md](./INSTALL.md)**.

Via [skills.sh](https://skills.sh) (marketplace público, sem clonar):

```bash
npx skills add Nomadiction8991/Skills              # todas as skills
npx skills add Nomadiction8991/Skills --skill commit  # só uma
```

Outros: `npx skills list` · `npx skills update Nomadiction8991/Skills` · `npx skills remove Nomadiction8991/Skills --skill <nome>`

## Uso

Quando o working directory estiver dentro deste repositório, qualquer menção a *skill* (criar, editar, ler, listar, refatorar) refere-se às skills deste repo — não é preciso especificar caminho externo.

O `AGENTS.md` na raiz garante que o agente entenda esse contexto automaticamente.

## Licença

MIT
