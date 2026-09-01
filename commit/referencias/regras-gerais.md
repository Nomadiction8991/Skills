# Referência — Regras Gerais (não negociáveis)

Regras que valem para **todo** commit criado por esta skill, sem exceção. Ler sempre antes de montar ou criar um commit — `fluxo.md` e `templates/commit.md` referenciam este arquivo em vez de repetir o conteúdo abaixo.

---

## 0. Nunca usar o contexto do chat para montar a mensagem

A mensagem do commit é derivada **exclusivamente** do estado real do repositório: `git status`, `git diff` (cached e não-cached), `git log` e leitura dos arquivos alterados. Histórico da conversa, resumos do assistente ou a forma como o usuário descreveu a mudança **nunca** são fonte da mensagem — se o diff não confirma, não entra no commit. Se o usuário sugerir um subject, validar contra o diff antes de aceitar.

---

## 1. O corpo da mensagem é sempre obrigatório

Nenhum commit fica só com a linha `tipo(escopo): descrição` — mesmo em mudanças pequenas ou que pareçam óbvias. Sempre escrever pelo menos um parágrafo de corpo explicando o quê mudou e por quê.

## 2. Corpo em linguagem simples, sem jargão técnico

Nada de nome de função/classe/variável, stack trace, nome de biblioteca/framework ou termo interno de implementação no corpo. Escrever como se explicasse pra alguém que não viu o código — mesmo sabendo que quem lê são desenvolvedores. Frases curtas, direto ao ponto, sem enrolação.

## 3. Nunca adicionar rodapé de atribuição de IA

Nunca incluir `Assistant-model:`, `Co-authored-by:` de assistente/IA, ou qualquer rodapé similar atribuindo o commit a uma ferramenta de IA. A especificação Conventional Commits permite esse tipo de rodapé, mas esta skill nunca deve usá-lo. Regra absoluta, sem exceção.

## 4. Nunca pular verificações de pre-commit

Se as verificações de pre-commit falharem, investigar e corrigir a causa raiz — nunca usar `--no-verify` ou qualquer outra forma de pular a verificação.

## 5. Vincular chamado do TomTicket (somente em projetos Ello) — buscar antes de perguntar

A vinculação de chamado do TomTicket só ocorre em **projetos do Ello** (detecção no Passo 0 do fluxo). Em qualquer outro projeto, **não perguntar** — seguir sem esta seção.

Em projeto Ello, se a skill `chamado` estiver disponível para o usuário (listada entre as skills instaladas):

1. **Buscar candidatos antes de perguntar:** usar a skill `chamado` para listar chamados prováveis — `mcp__tomticket__listar_chamados` (começar pelo status aberto; se o diff apontar um departamento específico, testar `departamento`). Filtrar/ranquear pelo contexto do diff (módulo, cliente, palavras-chave) e selecionar os 2–4 **títulos mais prováveis**.
2. **Apresentar os candidatos** ao usuário com os números, perguntando se o commit está relacionado a algum deles, e incluir a opção **"preencher manualmente"** para quando o chamado não estiver entre os listados.
3. Se o usuário escolher um candidato ou digitar o número: obter o **título** com a skill `chamado` (fluxo ler, `mcp__tomticket__ver_chamado`). A API não retorna o link do chamado (não pode ser inferido/gerado) — pedir o **link** diretamente ao usuário.
4. Se o usuário disser que não há chamado, seguir sem esta seção.

Quando houver chamado vinculado, o rodapé segue este formato (abaixo de outros rodapés, se houver):

```
Refs: #<número do chamado>
<Título do chamado> (<link do chamado>)
```

---

## 6. Subject com no máximo 80 caracteres

A linha do subject **nunca passa de 80 caracteres** — vale para qualquer projeto e modo, inclusive na forma conventional (`tipo(escopo): descrição`). Descrições curtas lêem melhor em listas de `git log`. Se passar de 80 no modo conventional, encurtar a descrição.

## 7. Corpo conciso — no máximo ~400 caracteres

O corpo não deve virar textão: **no máximo ~400 caracteres** (cerca de 6 linhas de ~72). Resumir ao essencial — o que mudou e por quê. Vale para qualquer projeto, inclusive na variante Ello (que tem corpo opcional).

## 8. Manter 1 commit por branch — usar `--amend` quando possível

Esta skill tenta manter **um único commit por branch**. Quando o usuário fizer novas alterações depois de um commit:

1. Verificar se as alterações atuais ainda são **válidas para o mesmo commit** — mesmo contexto/lógica do commit existente. Se forem outro trabalho, criar commit novo.
2. Verificar se o commit atual **já foi enviado ao remoto**: `git log origin/<branch>..HEAD` vazio (ou branch sem remota) → ainda não enviado.
3. **Não enviado** e mesmo contexto → fazer `git add` dos arquivos e `git commit --amend` (sem alterar a mensagem se ela ainda descreve bem as mudanças; editar a mensagem se o conteúdo mudou).
4. **Já enviado** → **nunca** usar amend (evita reescrever histórico remoto): criar commit novo e avisar o usuário.

## 9. Nunca executar `git commit` sem confirmação explícita do usuário

`git commit` (novo **ou** `--amend`) só pode ser executado depois que o usuário confirmar explicitamente — mostrar a **mensagem final pronta** e a **lista de arquivos** que entrarão no commit, e pedir `[S] Sim`/`[N] Não`. Sem exceção, mesmo quando o pedido do usuário parecer direto ou óbvio.

**Confirmar não é perguntar detalhe.** Perguntar antes sobre chamado, ticket ou changelog **não substitui** mostrar a mensagem final e pedir aprovação. A confirmação exigida aqui é sempre: exibir o conteúdo literal (subject + body + arquivos) e esperar aprovação explícita **desse conteúdo** — mesmo que outras perguntas já tenham sido respondidas.

Quando a regra #8 indicar `--amend`, mostrar também que será um **amend do commit anterior** (branch/mensagem atual) e para qual commit os arquivos vão. Se a mensagem for editada no amend, mostrar a mensagem nova completa na prévia.
