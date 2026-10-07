# Referência — Regras Gerais (não negociáveis)

Regras que valem para **todas** as operações de escrita desta skill (comentar, aprovar/desaprovar, criar, atualizar, merge). Não repetir este conteúdo em outro arquivo — sempre referenciar este.

---

## 1. Nenhuma ação de escrita sem prévia + confirmação explícita com opções

Vale para `create_merge_request_note`, `create_merge_request_discussion_note`, `approve_merge_request`, `unapprove_merge_request`, `create_merge_request`, `update_merge_request` e `merge_merge_request` — **sem exceção**, mesmo quando o pedido do usuário parecer direto, óbvio, ou já tiver sido detalhado antes.

**Sempre, antes de chamar a tool:**

1. Montar o conteúdo final exatamente como será enviado (texto do comentário, título/descrição da MR, campos que serão atualizados).
2. Exibir essa prévia por completo ao usuário — literal, não resumida.
3. Pedir confirmação explícita em formato de opções, por exemplo:
   - `[S] Sim, enviar` / `[N] Não, cancelar` / `[E] Editar antes`
   - ou, quando fizer sentido, opções mais específicas da operação (ex.: `[1] Comentário público` / `[2] Nota interna/discussion` / `[N] Cancelar`)

**Perguntar detalhe de configuração não é confirmar.** Perguntar "é discussion thread ou nota simples?" ou "qual branch de destino?" não substitui mostrar o conteúdo final e esperar `[S] Sim`. São perguntas diferentes.

**`merge_merge_request` é a ação mais crítica** — é a única de fato irreversível de forma prática (dá pra reverter um merge, mas exige nova ação/commit). Nunca executar merge só porque o usuário disse "pode mergear" sem mostrar de qual MR, para qual branch, e esperar confirmação explícita.

---

## 2. Nunca inferir número/IID de MR quando ambíguo

Se o usuário não informar o número da MR e houver mais de uma possível (ex.: "comenta na mr que corrigi isso" sem dizer qual), **listar as MRs candidatas e perguntar qual** em vez de adivinhar pela mais recente.

---

## 3. Escrita em MR (comentário/descrição) é técnica — não precisa "traduzir" para não-dev

Diferente de outras skills de suporte ao cliente, o público de comentários/descrições de MR é a equipe de desenvolvimento. Pode e deve conter nome de arquivo, branch, hash de commit, stack trace quando relevante. Ainda assim: claro, direto, sem ruído desnecessário.

---

## 4. Formato do texto

- Comentários e descrições de MR usam Markdown (formato nativo do GitLab), não HTML.
- Preservar formatação de código com blocos ```` ``` ```` quando citar trechos de código, comando ou stack trace.
