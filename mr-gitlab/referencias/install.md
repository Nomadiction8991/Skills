# Referência — Instalação/Autoinstalação do MCP GitLab

Esta skill depende do MCP server `gitlab` (pacote `@zereight/mcp-gitlab`). Antes de qualquer operação, verificar se ele já está instalado e conectado **nesta sessão**.

## 1. Verificar se já existe e está conectado

```bash
claude mcp list
```

- Se aparecer `gitlab: ... - ✔ Connected` → seguir direto para `toolsearch.md`, nada a instalar.
- Se aparecer `gitlab: ... - ✘` (erro) → é problema de configuração/token existente, não falta de instalação. Avisar o usuário do erro específico (ex: 401, comando não encontrado) e perguntar como prosseguir — não tentar adivinhar/corrigir credencial sozinho.
- Se **não aparecer nenhum servidor `gitlab`** → seguir para o passo 2 (autoinstalação).

## 2. Autoinstalação (quando o MCP `gitlab` não existe em nenhum escopo)

**Nunca hardcodar ou escrever um token de GitLab dentro de arquivos desta skill** (SKILL.md, referencias/*, templates/*). Esses arquivos podem ser versionados/compartilhados — gravar um Personal Access Token aqui seria vazar uma credencial. O token só pode existir dentro do comando `claude mcp add` executado via Bash (que grava em `~/.claude.json`, fora do controle desta skill) ou nas variáveis de ambiente do processo.

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
