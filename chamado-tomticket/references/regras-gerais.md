# Referência — Regras Gerais (não negociáveis)

Regras que valem para **todas** as operações desta skill (abrir, editar, comentar, transferir — e o que mais vier a existir), não só para um fluxo específico. Os arquivos de cada fluxo (`abrir.md`, `editar.md`, `comentar.md`, `transferir.md`) devem referenciar este arquivo em vez de repetir o conteúdo abaixo.

---

## 1. Nunca executar ação de escrita sem confirmação explícita do usuário

`criar_chamado`, `editar_chamado`, `comentar_chamado` e `transferir_chamado` só podem ser chamadas depois que o usuário confirmar explicitamente o conteúdo final (`[S] Sim`). Sem exceção, mesmo quando o pedido do usuário parecer direto ou óbvio.

**Confirmar não é perguntar detalhe de configuração.** Perguntar algo como "nota interna ou resposta ao cliente?" ou "qual departamento?" **não substitui** mostrar o texto final e pedir `[S] Sim`/`[N] Não`. São perguntas diferentes — responder uma não é confirmar a outra. A confirmação exigida por esta regra é sempre: exibir o conteúdo literal que vai para a API (título/mensagem/comentário já prontos) e esperar aprovação explícita **desse conteúdo**, mesmo que outras perguntas já tenham sido respondidas antes.

**`comentar_chamado` é a ação mais crítica desta regra.** Comentário postado no TomTicket **não pode ser editado nem apagado depois** (nenhuma tool desta skill faz isso, e não há confirmação de que existe outro caminho) — é a única ação irreversível entre as quatro. Errar aqui não tem como desfazer, então pular a confirmação em um comentário é sempre pior do que pular em título/descrição (que pelo menos são editáveis depois). Nunca tratar "já sei o que o usuário quer" como motivo pra pular a prévia do comentário.

> **Erro real já cometido (chamado #3975):** o assistente perguntou só "nota interna ou visível ao cliente?", tratou a resposta como confirmação, e chamou `comentar_chamado` sem nunca ter mostrado o texto do comentário. O comentário já foi publicado e não pôde ser revertido.

---

## 2. A mensagem enviada à API é sempre HTML real — nunca Markdown ou texto puro

Vale para o campo `mensagem` em `criar_chamado`, `editar_chamado`, `comentar_chamado` e `transferir_chamado` (quando preenchido).

- O que o usuário vê na prévia/preview antes de confirmar pode (e deve) ser Markdown legível — isso é só representação visual.
- O que de fato é enviado para a API é sempre HTML montado seguindo `ferramentas.md` (`<h4><span style="...">`, `<ol>/<ul>` reais, `<p>` para parágrafos).
- Nunca confundir as duas coisas: preview em Markdown ≠ payload em HTML. Montar o HTML de verdade antes de chamar a tool, não só formatar a prévia.
- Antes de qualquer chamada de escrita, validar o HTML contra a checklist de `checklist-html.md` (heading com `style` teria que estar dentro de um `<span>` interno — o Froala descarta silenciosamente `style` direto no `<h1>`–`<h4>`, ver regra crítica em `ferramentas.md`).

---

## 3. Título e corpo do chamado (criação/edição) descrevem só o problema, nunca a solução

Os itens 1 e 2 abaixo (descrever só o problema, nunca narrar a solução) valem só para `titulo` e `mensagem` em `criar_chamado`/`editar_chamado` — **não** se aplicam a `comentar_chamado`, que é justamente o lugar certo para falar sobre a correção aplicada.

Já os itens 3 e 4 (escrever para o suporte, sem jargão, claro e conciso) valem **para toda escrita da skill, incluindo `comentar_chamado`** — ver nota logo abaixo da lista.

1. **Descreva o problema, não a solução.** O chamado registra o que está errado / o que o usuário precisa — nunca como resolver, qual correção aplicar, qual commit, qual código mudar. Diagnóstico e solução são do time que atende.
2. **Mesmo quando o chamado nasce a partir de um commit/PR já pronto, já em produção, ou está sendo editado depois que a correção já existe, escreva/mantenha como defeito ou melhoria em aberto — nunca como algo já implementado/corrigido.** Um chamado normalmente nasce ANTES da correção existir, e continua descrevendo o problema em aberto mesmo depois. Não narrar "isso foi implementado", "comportamento corrigido", "antes disso era possível X", "corrigido para X", "ajustado Y para Z", "refeito em N branches" — isso descreve a solução, não o problema.
   - **A regra vale para o texto inteiro, inclusive dentro de listas e sub-itens** — não só no parágrafo de abertura da Descrição. É comum a violação ficar escondida dentro de um item de lista que mistura problema + solução na mesma frase.
   - ❌ **Errado** (mistura defeito com a correção aplicada): `"Erro 500 em valor ≥ R$ 1.000 — number_format usava vírgula como separador de milhar, gerando string inválida. Corrigido para number_format(...)."`
   - ✅ **Certo** (só o defeito, sem a solução): `"Erro 500 em valor ≥ R$ 1.000 — separador de milhar tratado incorretamente ao salvar, valor não é aceito pelo sistema."`
   - Títulos de seção/lista também não podem carregar o resultado da correção: usar "Defeitos identificados", nunca "Defeitos corrigidos" (a palavra "corrigidos" já é a solução).
   - Termos como "corrigido", "ajustado", "implementado", "resolvido" **não podem aparecer em nenhum lugar do título ou da mensagem** do chamado — só em comentários.
   - **Não existe seção "Correção"/"Solução" no corpo do chamado.** Criar uma seção dedicada pra falar de branch/commit/teste é o mesmo erro só disfarçado de seção separada. Falar sobre a correção só existe em **comentário** (`comentar_chamado`), nunca na mensagem principal — mas o comentário também segue os itens 3 e 4 abaixo (sem jargão, linguagem simples), não é um espaço livre pra colar detalhe cru de commit/branch/arquivo.
   - Isso vale mesmo ao editar um chamado só porque a abordagem técnica mudou (ex.: uma correção que era 1 branch virou 3 branches): o texto do *problema* não muda porque a solução mudou — se não há nada de novo sobre o problema para editar, a resposta correta é **não editar o corpo do chamado**, e o detalhe técnico (se fizer sentido registrar) vai em comentário.
   - **Erros reais já cometidos (referência, chamado #3833):** editar a Descrição para dizer "Refeitos como 3 branches separadas, cada uma com testes próprios"; e criar/manter uma seção "Correção" no corpo achando que era o "lugar certo" — ambos o mesmo erro.
3. **Escreva para o suporte, não para o dev — vale também em comentário.** Quem lê título/corpo/comentário é a equipe de suporte, não outro desenvolvedor. Sem jargão técnico de desenvolvimento: nada de hash de commit, nome de branch, nome de arquivo/classe/método, rotas, tabelas, colunas, HTTP status, stack trace ou termos de framework — em nenhuma das quatro operações (`criar_chamado`, `editar_chamado`, `comentar_chamado`, `transferir_chamado`). Descreva pelo que o usuário vê na tela e faz no sistema; em comentário, descreva o que foi corrigido em termos de comportamento observável, não a implementação.
4. **Claro, simples e conciso.** Frases curtas e diretas. Só o necessário para entender (e, em título/descrição, reproduzir) o problema. Sem encher de detalhe técnico ou contexto irrelevante.

> **Erro real já cometido (chamado #3975):** um comentário de correção foi escrito citando hash de commit, nome de branch e nomes de arquivo (`Dockerfiles/web/Dockerfile`, `docker-compose.yml`), em vez de descrever a causa e o efeito em linguagem simples. O item 3 vale mesmo quando o comentário é nota interna — "interno" não significa "só pra dev".

Antes de confirmar com o usuário (criação/edição) ou exibir a prévia (comentário), revisar o texto inteiro contra os itens 3 e 4 — se sobrar hash de commit, nome de branch, nome de arquivo, ou jargão de framework, reescrever em linguagem simples antes de mostrar a prévia.
