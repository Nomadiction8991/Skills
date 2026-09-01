# Referência — Fluxo do Commit

Passo a passo completo para criar um commit bem formatado.

Ver `regras-gerais.md` — vale integralmente para a mensagem montada neste fluxo.

---

## Passo 0 — Detectar projeto Ello

Antes de qualquer coisa, verifique se o projeto atual é do **Ello ERP**:

1. `git rev-parse --show-toplevel` → se o **caminho** ou o **nome** do repositório contiver "ello" (case-insensitive), é projeto Ello.
2. Caso contrário, leia `AGENTS.md` e `README.md` da raiz do projeto → se citarem "Ello" (ex.: "Ello ERP"), é projeto Ello.

**Se for Ello:** monte a mensagem com `referencias/ello.md` e `templates/ello-commit.md` — a variante substitui o subject, o corpo e o rodapé TomTicket. Pule o Passo 4 de Conventional Commits. As demais regras gerais (limites de tamanho, rodapé de IA, pre-commit) continuam valendo.

---

## Passo 1 — Verificar Staging

Checar `git status` (já injetado no SKILL.md, seção "Estado Atual do Repositório").

- Se **houver** arquivos em staging: commitar **apenas** esses arquivos — não adicionar mais nada.
- Se **não houver** nenhum arquivo em staging: adicionar automaticamente todos os arquivos modificados e novos com `git add`.

---

## Passo 1.5 — Verificar amend (1 commit por branch)

Se a branch **já tem commit** (HEAD não é o commit inicial da branch) e há novas alterações:

1. Verificar se o último commit **já foi enviado ao remoto**: `git log --oneline origin/<branch>..HEAD`. Se a branch não tem remota ou o log mostra commits → ainda não enviado.
2. Verificar se as alterações atuais são do **mesmo trabalho** do commit existente (mesmo contexto/lógica). Se forem outro trabalho → commit novo.
3. **Não enviado + mesmo trabalho** → informar o usuário que será um amend, fazer `git add` dos arquivos e `git commit --amend`. Reavaliar o diff completo (commit anterior + novos) e **editar a mensagem se necessário** para descrever o conjunto final; se a mensagem atual já cobre, mantê-la.
4. **Já enviado** → não usar amend (regra geral #8): criar commit novo e avisar.

Em seguida, seguir para o Passo 2 com o diff resultante.

---

## Passo 2 — Analisar o Diff

Executar `git diff --cached` (ou `git diff` se acabou de fazer `git add`) para entender exatamente o que está sendo commitado — nunca escrever a mensagem só pelo `git status`/nomes de arquivo.

Analisar se há **múltiplas alterações lógicas distintas** no diff (ex.: uma refatoração + uma feature nova, ou mudanças em dois módulos não relacionados).

- Se detectar múltiplas alterações distintas, **sugerir dividir em commits menores** antes de commitar, e perguntar ao usuário se prefere separar ou manter tudo junto.
- Se for uma alteração lógica única (mesmo que toque vários arquivos), seguir com um commit só.

---

## Passo 3 — Verificar Chamado Vinculado (só em projeto Ello)

Aplicar `regras-gerais.md` (regra #5): **somente** em projeto Ello (Passo 0) — se a skill `chamado` estiver disponível, perguntar se este commit está relacionado a um chamado do TomTicket antes de montar a mensagem. Em projetos não-Ello, **pular** esta verificação.

---

## Passo 3.5 — Changelog no deploy (só em projeto Ello)

Em projeto Ello (Passo 0), **pergunte ao usuário** se o commit deve ou não
aparecer no changelog de deploy — nunca assuma. Aplicar a regra do ` *` da
seção 2 de `ello.md`:

- **Há ticket** (`tt-XXXX` detectado) → subject termina com `(tt-XXXX)`,
  **sem** ` *` no final.
- **Sem ticket e deve sair no changelog** → subject **sem** ` *`.
- **Sem ticket e NÃO deve sair no changelog** → subject termina com ` *`.

Regras da pergunta:

- Pergunte o changelog **sempre**, mesmo com ticket — confirme com o
  usuário que a decisão está correta (o `(tt-XXXX)` já implica "sai no
  changelog"; se o usuário disser que não deve sair, só comente a exceção).
- Formule a pergunta com as opções claras: "sai no changelog" / "não sai
  no changelog" (e "tem ticket" quando aplicável), com a recomendada.
- Se não for possível perguntar (ambiente não interativo), assuma "deve
  sair no changelog" (sem ` *`).

---

## Passo 4 — Montar a Mensagem

**Se for projeto Ello (Passo 0):** usar `../templates/ello-commit.md` com as regras de `../referencias/ello.md`.

**Senão:** usar o template de `../templates/commit.md`, preenchendo com base no diff real (Passo 2) — nunca a partir do pedido do usuário isoladamente, nem só dos nomes dos arquivos alterados. Seguir a especificação Conventional Commits (`conventional-commits.md`) para tipo, escopo e formato, e `regras-gerais.md` para corpo/linguagem/atribuição/chamado vinculado.

---

## Passo 5 — Criar o Commit (com confirmação)

Para cada commit (ou o único, se não dividido no Passo 2), **antes de executar qualquer `git commit`** (regra geral #9):

1. Exibir a prévia: **mensagem final pronta** (subject + body) e a **lista de arquivos** que entrarão no commit.
2. Se for `--amend` (Passo 1.5), informar isso e qual commit será alterado.
3. Pedir confirmação explícita `[S] Sim` / `[N] Não` e aguardar a resposta.
4. Só com `[S]`, executar `git commit` (ou `git commit --amend`) com a mensagem montada.

Ver `regras-gerais.md` (regras #3, #4 e #9). Ao final, revisar o diff do commit para garantir que a mensagem corresponde exatamente às alterações.
