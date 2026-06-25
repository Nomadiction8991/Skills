---
name: commit-ello
model: haiku
allowed-tools: Read Bash
description: >
  Gera mensagem de commit Git seguindo os padrões do projeto Ello ERP.
  Use sempre que o usuário pedir "gera commit", "mensagem de commit",
  "escreve commit", "cria commit", "message de commit", "commit message",
  "que mensagem coloco no commit", "como commitar isso", ou quando o
  usuário terminar uma alteração e quiser registrá-la no git. A skill
  lê o diff staged/unstaged e o histórico recente para produzir uma
  mensagem em Português brasileiro no estilo do projeto: verbo no
  presente, body explicativo quando necessário, referência ao ticket
  no subject, e trailer Co-Authored-By com o modelo atual.
---

# commit-ello

Gera mensagem de commit no padrão do Ello ERP. Siga os passos abaixo sempre que ativado.

## Passo 1 — Coletar o diff e o contexto

Primeiro, descubra a raiz do repositório com:
```bash
git rev-parse --show-toplevel
```

Use o caminho retornado como `workdir` nos comandos abaixo. Execute em bash:

```bash
git diff --cached --stat
git diff --cached
git log --oneline -8
```

Se `git diff --cached` estiver vazio (nenhum arquivo em staged), use em vez disso:

```bash
git diff HEAD --stat
git diff HEAD
```

O `git log --oneline -8` serve para entender o ritmo e o estilo dos commits recentes — se há uma série em andamento, se o padrão de subject é mais curto ou mais descritivo, etc.

## Passo 2 — Analisar as mudanças

A partir do diff, identifique:

- **O que mudou**: arquivos, funções, classes, queries, configurações
- **O tipo de mudança**: bugfix, feature, refatoração, melhoria de perf, atualização de deps/tools
- **O motivo** (o *porquê*, não o *o quê*): o que estava errado, o que faltava, qual ganho a mudança traz
- **Root cause** para bugfixes: o que causava o comportamento errado
- **Número de ticket**: procure padrões como `tt-XXXX` ou `TT-XXXX` na mensagem do usuário ou no diff (comentários, strings de erro, nomes de branch)

## Passo 3 — Perguntar ao usuário

Antes de montar a mensagem, faça **duas perguntas** em uma única mensagem:

1. **Ticket**: "Há um número de ticket vinculado? (ex: tt-1234)" — se o usuário já mencionou o número anteriormente, use-o e pule esta pergunta.
2. **Changelog** (só perguntar se **não** houver ticket): "Este commit deve aparecer no changelog do deploy?"

Aguarde a resposta antes de prosseguir.

## Passo 4 — Montar a mensagem

### Regra do `*` no subject

O `*` no final do subject **exclui o commit do changelog de deploy**. Use a seguinte lógica:

- **Há ticket** → inclui `(tt-XXXX)` no final do subject, **sem** ` *`
- **Não há ticket e deve sair no changelog** → **sem** ` *`
- **Não há ticket e NÃO deve sair no changelog** → termina com ` *`

### Subject (sempre presente)

- Começa com **verbo no presente**: *Corrige, Implementa, Adiciona, Remove, Extrai, Substitui, Revisa, Melhoria, Atualiza, Bloqueia, Registra, Oculta...*
- Máximo 80 caracteres
- Português brasileiro com acentos
- **Seja específico sobre o contexto**: mencione **onde** a mudança afeta (qual tela, qual módulo, qual feature)
  - Preferir **o título da tela** ao invés do código (EST200AA → "Listagem de Produtos")
  - ❌ "Oculta coluna Nota lançada do usuário contador"
  - ✅ "Oculta coluna Nota lançada para módulo contador na tela de MD-e"
- Preferir **"para contador"** / **"para empresa"** em vez de **"do usuário contador"** quando for modo específico (mais objetivo e técnico)
- **Evitar palavras vagas**: "semântica especial", "tratamento especial" — seja concreto sobre o quê é diferenciado
  - ❌ "Implementa tratamento de manifestação com semântica especial no modo contador"
  - ✅ "Implementa ciência e confirmação diferenciadas para contador na MD-e"
- Para refatorações que alteram comportamento: nomear o **resultado** (o que agora NÃO acontece) pode ser melhor que nomear a refatoração
  - ❌ "Unifica em GravaEvento a regra de manifestacao"
  - ✅ "Não grava ciência sem resumo na Manifestação do destinatário" (deixa claro o novo comportamento)

### Body (use quando a mudança for complexa ou for um bugfix)

Escreva o body quando qualquer uma das condições for verdadeira:
- É um bugfix (o leitor precisa entender o root cause)
- Mudança em mais de 2–3 arquivos com lógica não trivial
- Envolve decisão de design não óbvia (por que esta abordagem e não outra)
- Feature nova com impacto relevante no comportamento do sistema

**Estrutura do body:**

1. Parágrafo de contexto: o que estava errado ou o que motivou a mudança
2. Parágrafo de solução (opcional): como foi resolvido, o que mudou e por quê essa abordagem
3. Detalhes técnicos relevantes: nomes de funções, classes, queries, componentes afetados

Regras de formato:
- Linha em branco separando subject do body
- Linhas de no máximo ~72 caracteres
- Português com acentos
- Pode usar bullets (`-`) para listar múltiplas mudanças independentes

### Trailer (sempre presente)

Linha em branco após o último parágrafo (ou após o subject quando não há body), seguida do trailer com o modelo atual da sessão:

```
Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>
```

Use o modelo identificado no system prompt da sessão atual. Exemplos de nomes:
- `Claude Opus 4.8 (1M context)` → para sessões Opus com contexto longo
- `Claude Opus 4.8`
- `Claude Sonnet 4.6`
- `Claude Haiku 4.5`

## Exemplos do projeto

**Bugfix com ticket (entra no changelog, sem `*`):**
```
Corrige comissão negativa de devolução não gravada (TT-3561)

GravaComissaoNegativa calculava TTDif com FieldByName desqualificado
dentro de `with Query do`. O identificador resolvia para a própria
Query interna, resultando em Lib - Lib = 0 sempre — a comissão
negativa nunca era gravada em TVdaComissaoLiberada.

A consulta externa agora é nomeada (QueryPedido) e referenciada
explicitamente, corrigindo o cálculo para Pdt - Lib.

Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>
```

**Refatoração interna (não entra no changelog, com `*`):**
```
Extrai TDFeConfiguracoesDistribuicao e a usa internamente em TDistribuicaoDFe *

TDistribuicaoDFe.Create delega a configuração do ACBr para a nova
classe em vez de montá-la inline. Versão do layout, pastas de XML,
ambiente e separação por ano/mês ficam centralizados em
ConfiguraACBr sobrescrito. A API pública de TDistribuicaoDFe não muda.

Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>
```

**Feature que deve sair no changelog (sem ticket, sem `*`):**
```
Melhoria na performance da listagem de produtos

Elimina consultas N+1 no CalcFields da tela de listagem de produtos.
O OnCalcFields executava DadosAtualizadosProduto a cada linha visível
do grid em todo repaint (~20 queries por repaint). Os campos
necessários agora vêm na query principal via join, zerando as
consultas durante o scroll.

Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>
```

**Mudança de infra/tools (não entra no changelog, com `*`):**
```
Remove código morto *

Co-Authored-By: Claude Haiku 4.5 <noreply@anthropic.com>
```

## Passo 5 — Apresentar o resultado

Apresente **apenas** a mensagem em um bloco de código, pronta para copiar. Não explique o formato nem o raciocínio — o usuário já conhece o padrão.

Se o diff estiver vazio (nada staged nem unstaged), informe o usuário que não há mudanças para commitar.
