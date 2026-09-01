# Referência — Variante Ello (projetos do Ello ERP)

Quando o projeto atual for detectado como Ello (Passo 0 de `fluxo.md`), esta variante **substitui** os padrões da skill `commit`: o subject, o corpo, o template e o chamado TomTicket. As regras gerais de processo (staging, diff, divisão de commits, pre-commit) e a regra de rodapé de IA continuam valendo.

---

## 1. Subject — padrão Ello (não usa Conventional Commits)

O subject **não** leva o prefixo `tipo(escopo):` do Conventional Commits. Começa com **verbo no presente** (Corrige, Implementa, Adiciona, Remove, Extrai, Substitui, Revisa, Melhoria, Atualiza, Bloqueia, Registra, Oculta...) e segue com o contexto:

- Máximo 80 caracteres
- Português brasileiro com acentos
- Sem ponto final

Dicas de estilo:

- Preferir o **título da tela** ao código da tela (EST200AA → "Listagem de Produtos")
  - ❌ "Oculta coluna Nota lançada do usuário contador"
  - ✅ "Oculta coluna Nota lançada para módulo contador na tela de MD-e"
- Preferir **"para contador"** / **"para empresa"** em vez de **"do usuário contador"** quando for modo específico (mais objetivo e técnico)
- Evitar palavras vagas: "semântica especial", "tratamento especial" — ser concreto sobre o quê é diferenciado
  - ❌ "Implementa tratamento de manifestação com semântica especial no modo contador"
  - ✅ "Implementa ciência e confirmação diferenciadas para contador na MD-e"
- Refatoração que altera comportamento: nomear o **resultado** (o que agora NÃO acontece) pode ser melhor que nomear a refatoração
  - ❌ "Unifica em GravaEvento a regra de manifestacao"
  - ✅ "Não grava ciência sem resumo na Manifestação do destinatário"

---

## 2. Ticket e a regra do `*` no subject

O ` *` no final do subject **exclui o commit do changelog de deploy**. Lógica:

- **Há ticket** → inclui `(tt-XXXX)` no final do subject, **sem** ` *`
- **Não há ticket e deve sair no changelog** → **sem** ` *`
- **Não há ticket e NÃO deve sair no changelog** → termina com ` *`

Identificação do ticket: procurar padrões `tt-XXXX` / `TT-XXXX` na mensagem do usuário, no diff (comentários, strings de erro) ou no nome da branch.

**Sempre pergunte ao usuário** se o commit sai ou não no changelog de deploy (passo 3.5 do `fluxo.md`) — não assuma pela presença do ticket. A pergunta vale inclusive com ticket (o `(tt-XXXX)` normalmente implica "sai no changelog"; confirme, e se o usuário disser que não deve sair, aplique a exceção conforme a tabela acima).

---

## 3. Corpo — opcional, linguagem simples (substitui a regra #1 da skill)

No Ello o corpo **é opcional** (na skill geral é sempre obrigatório) e segue o mesmo espírito de linguagem simples:

- **Preferir linguagem simples e fácil de ler**, mesmo sabendo que quem lê são desenvolvedores. Explicar como quem não viu o código.
- **Jargão técnico** (nomes de funções, classes, queries, componentes): **pode usar alguns termos técnicos — só não encher**. O critério é o leitor: jargão que facilita o entendimento é bem-vindo (ex.: citar `GravaComissaoNegativa` num bugfix é mais preciso do que contornar de outra forma); jargão que atrapalha ou não agrega, não cite. Se o body ficar repleto de nomes internos, simplifique.

Ocorre apenas quando for necessário:
- É um bugfix (o leitor precisa do root cause)
- Mudança em mais de 2–3 arquivos com lógica não trivial
- Decisão de design não óbvia (por que esta abordagem e não outra)
- Feature nova com impacto relevante no comportamento do sistema

Estrutura:
1. Parágrafo de contexto: o que estava errado ou o que motivou a mudança
2. Parágrafo de solução (opcional): como foi resolvido, o que mudou e por quê essa abordagem
3. Detalhes técnicos (opcional, só se facilitarem o entendimento — ver regra de jargão acima)

Formatação:
- Linha em branco separando subject do body
- **Conciso: no máximo ~400 caracteres** (regra geral #7)
- Linhas de no máximo ~72 caracteres
- Português com acentos
- Bullets (`-`) para listar múltiplas mudanças independentes

---

## 4. Nunca rodapé de atribuição de IA

Reforço da regra geral #3 (`regras-gerais.md`): **nunca** incluir `Co-Authored-By`, `Assistant-model:` nem qualquer rodapé que atribua o commit a uma IA. O template Ello não tem seção de trailer. Absoluto, sem exceção.

---

## 5. Chamado TomTicket (regra #5 aplicada no Ello)

O ticket do Ello vai no **subject** como `(tt-XXXX)`. Além dele, a regra geral #5 vale em projetos Ello: perguntar se o commit está relacionado a um **chamado TomTicket** (via skill `chamado`) — se houver, usar o rodapé `Refs: #<número>` + título e link abaixo do subject.