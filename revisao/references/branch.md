# Modo Branch — /revisao branch [base]

Revisa a branch atual (`HEAD`) inteira contra a base/pai explícita ou detectada conforme `regras.md`. Sempre chama agents com contexto limpo. Nunca posta sem permissão explícita de uso único (`regras.md`). Captura do diff conforme `processo.md#1`.

## 1. Ler arquivos completos + diff da branch

Abrir arquivos inteiros tocados + diff completo da branch (`git diff <base>...HEAD`). O diff isolado esconde validação do irmão e padrão dos vizinhos.

## 2. Lançar agents (contexto limpo)

Disparar em paralelo com `general-purpose`, cada um com diff + lista de commits + arquivos completos:

**Agent A — Autorização, divergência e borda:**
Briefing: "Verifique autorização/escopo (validacoes#1), mesma informação divergente (#2), borda dos dados (#6) e separação refatoração vs feature (#8). Compare com método irmão que já valida. Até 350 palavras, linguagem simples pelo efeito."

**Agent B — Código morto, responsabilidade, padrões e infra:**
Briefing: "Verifique código morto (#3), indireção (#4), duplicação (#5), responsabilidade (#6), padrões do stack (#9), infra (#10), pipeline/config (#11), migração (#12), idioma consistente (#13) e rodar testes (#14). Use `validacoes.md` + `code-smells.md`. Até 400 palavras, linguagem simples."

**Agent C — Consistência e timers (se houver frontend):**
Briefing: "Verifique consistência com vizinhos (nomenclatura, camadas, CLAUDE.md) e estado assíncrono/timers (#4 validacoes + checklist#4). Até 250 palavras."

Cada agent recebe `validacoes.md` colada — não precisa ler de novo. São 2–3 agents conforme o diff (se não tem frontend, pular C).

## 3. Validar antes de agregar

Mesmo que modo branch: confirmar internamente o arquivo e a linha no código atual, além do cenário concreto e da relação direta com o diff; não mostrar esses detalhes no relatório. Descartar problemas pré-existentes ou fora do escopo, sem inflar severidade.

## 4. Agregar

Seguir `formato-saida.md` + `processo.md#5`. Mostrar o relatório ao usuário, sempre incluindo o `Resumo final` antes da pergunta final. Nunca commitar, corrigir ou postar durante a revisão.
