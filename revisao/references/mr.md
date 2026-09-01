# Modo MR — /revisao mr [base] e /revisao mr <numero>

Revisa a branch inteira. Sempre chama agents com contexto limpo. Nunca posta sem permissão explícita de uso único (`regras.md`). Captura do diff conforme `processo.md#1`.

## 1. Ler arquivos completos + diff da MR

Abrir arquivos inteiros tocados + diff completo da MR (discussions/diffs via MCP). O diff isolado esconde validação do irmão e padrão dos vizinhos.

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

Mesmo que modo commit: confirmar arquivo:linha no código atual, cenário concreto, marcar pré-existente, não inflar severidade. Para `mr <numero>`, verificar se achado já foi comentado na MR antes.

## 4. Agregar e postar

Seguir `formato-saida.md` + `processo.md#5`. Mostrar texto exato no terminal e perguntar `Posso postar no MR <numero>? [S/N]` — só após `S` explícito naquela vez, postar via MCP `gitlab` (permissão expira). Nunca commitar/corrigir durante a revisão.
