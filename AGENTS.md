# Skills — Repositório Versionado

> **ATENÇÃO AGENTE:** Este diretório (`/home/weverton/Documentos/Projetos/Skills`) é o **repositório versionado de skills** do usuário. Toda skill aqui é código versionado.

## Regra de Desambiguação (obrigatória)

Quando o **working directory** (`cwd`) estiver dentro deste workspace — ou qualquer subpasta dele — e o usuário disser qualquer coisa relacionada a **skill** em linguagem natural, **assuma que ele está falando das skills deste repo**, salvo se ele disser explicitamente um caminho externo ou outro projeto.

Gatilhos que ativam esta regra (com ou sem `/`, em PT-BR natural):

- `skill`, `skills`, `criar skill`, `nova skill`, `editar skill`, `alterar skill`, `ler skill`, `ver skill`, `listar skills`, `remover skill`, `deletar skill`, `refatorar skill`, `padronizar skill`, `corrigir skill`, `revisar skill`, `atualizar skill`, `SKILL.md`

Ou seja: se o usuário está neste workspace e pede *"cria uma skill que faz X"*, *"edita a skill Y"*, *"lê a skill Z"*, *"quais skills temos?"* — **não pergunte de qual repo**, não procure em outro lugar, opere diretamente sobre as pastas deste diretório.

## Estrutura do Repo

```
./
├── chamado-tomticket/   # skill TomTicket
├── commit/              # skill commit (conventional commits)
├── linguagem/
├── manter-sessao/
├── mr-gitlab/
└── revisao/
```

Cada skill = uma pasta + `SKILL.md` na raiz da pasta + `references/` e `templates/` quando aplicável.

- `SKILL.md` — roteia por `args` e aponta para referências (enxuto)
- `references/*.md` — regras e fluxos modulares (ler só o que o modo pede)
- `templates/*.md` — templates de saída

## O que fazer quando a regra ativar

1. **Listar/ler:** use `Read`/`Glob`/`Grep` diretamente neste diretório para localizar a skill alvo.
2. **Editar:** leia o `SKILL.md` e as referências afetadas antes de editar. Mantenha o `SKILL.md` enxuto.

## Skills existentes (referência rápida)

| Skill | Gatilho | Descrição |
|-------|---------|-----------|
| `chamado-tomticket` | `/chamado-tomticket` | Gerencia chamados TomTicket |
| `commit` | `/commit` | Cria commits conventional commits |
| `linguagem` | `/linguagem` | — |
| `manter-sessao` | `/manter-sessao` | — |
| `mr-gitlab` | `/mr-gitlab` | Gerencia MRs GitLab |
| `revisao` | `/revisao` | — |
