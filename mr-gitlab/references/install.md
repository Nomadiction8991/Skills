# Referência — Detectar acesso ao GitLab (MCP ou glab) e autoinstalação

A skill tem duas vias para o GitLab: o MCP `gitlab` (pacote `@zereight/mcp-gitlab`) e o `glab` (CLI oficial do GitLab). Antes de qualquer operação, descobrir qual está disponível **nesta sessão**. Só dizer "sem acesso ao GitLab" depois de testar as duas.

## 1. Detectar a via (nesta ordem, para na primeira que funcionar)

```bash
claude mcp list 2>/dev/null | grep -i gitlab
command -v glab && glab auth status 2>&1 | head -5
```

1. **MCP:** `gitlab: ... - ✔ Connected` **e** o `ToolSearch` carrega as tools `mcp__gitlab__*` → usar MCP. Seguir para `toolsearch.md`.
2. **glab:** MCP ausente, com erro ou sem tools nesta sessão, mas `glab auth status` mostra `✓ Logged in to <host>` → usar `glab`. Seguir `glab.md`. Não instalar MCP, não pedir token, não pedir sessão nova. Avisar em uma linha: "MCP do GitLab não está conectado; usando o glab (logado como <usuário>)."
   - Conferir que o host logado bate com o host do remote do repo (`git remote get-url origin`). Se não bater, tratar como sem glab.
3. **MCP com erro e sem glab:** `gitlab: ... - ✘` → problema de configuração/token existente. Avisar o erro específico (ex: 401) e perguntar como prosseguir — não adivinhar/corrigir credencial sozinho.
4. **Nenhum dos dois:** seguir para o passo 2 (autoinstalação do MCP). Mencionar que instalar e logar o `glab` (`glab auth login`) também resolve, sem precisar de sessão nova.

## 2. Autoinstalação (quando não há MCP `gitlab` em nenhum escopo nem `glab` logado)

**Nunca hardcodar ou escrever um token de GitLab dentro de arquivos desta skill** (SKILL.md, references/*, templates/*). Esses arquivos podem ser versionados/compartilhados — gravar um Personal Access Token aqui seria vazar uma credencial. O token só pode existir dentro do comando `claude mcp add` executado via Bash (que grava em `~/.claude.json`, fora do controle desta skill) ou nas variáveis de ambiente do processo.

1. **URL da API do GitLab** — usar sempre `https://gitlab.com/api/v4` direto, sem perguntar. Só perguntar a URL correta se o usuário mencionar espontaneamente instância self-hosted/própria.
2. **Personal Access Token** — perguntar sempre ao usuário (escopo mínimo necessário: `api` para ler/comentar/aprovar MRs). Nunca reaproveitar token de outro projeto automaticamente.
3. Escopo de instalação é **sempre global** (`-s user`) — não perguntar, não oferecer escopo de projeto.
4. Rodar direto, sem mostrar prévia nem pedir confirmação — só instalar e verificar se funcionou:

```bash
claude mcp add gitlab -s user \
  --env GITLAB_PERSONAL_ACCESS_TOKEN="<token informado pelo usuário>" \
  --env GITLAB_API_URL="<url informada pelo usuário>" \
  -- npx -y @zereight/mcp-gitlab
```

5. Conferir que a configuração foi gravada corretamente com `claude mcp get gitlab` (sem exibir o token na resposta). Se o comando falhar ou a config sair errada, corrigir e tentar de novo até funcionar.
6. Avisar o usuário que **o MCP só carrega numa sessão nova** — o servidor não aparece disponível na sessão atual mesmo depois do `claude mcp add`. Sugerir reiniciar/abrir nova sessão antes de tentar usar as tools de MR.

## 3. Nunca pedir para o usuário colar o token em texto puro na conversa sem necessidade

Se o usuário fornecer o token diretamente na mensagem, ele já ficará no histórico da conversa de qualquer forma — isso é aceitável (é a via padrão do Claude Code), mas a skill nunca deve **repetir o token de volta** na resposta, nem gravá-lo em nenhum arquivo dentro deste diretório de skills.
