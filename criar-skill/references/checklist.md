# Checklist — Validação da Skill Criada/Refatorada

Passe por todos antes de pedir permissão para escrever. Marque ok ou reprove com arquivo:linha.

## 1. Nome e pasta
- Pasta `plugins/fluxo-trabalho/skills/<nome>/` existe e `name` no frontmatter == pasta (lowercase-hyphen, ≤64 chars)?

## 2. Frontmatter
- `name`, `description` (com "Use via /<nome> ou sempre que..." + gatilhos em linguagem natural), `argument-hint`, `allowed-tools` mínimo, `model`/`effort` adequados?
- `description` sem jargão preso a stack, genérica para qualquer projeto?

## 3. SKILL.md enxuto
- Só roteamento por `args` + lista de `references/*.md`? Sem duplicar regras/checklist/formato que já estão nas referências?
- Cada `args` aponta para 1–2 referências específicas (leitura seletiva)?

## 4. Referências modulares
- `regras-gerais.md` é fonte da verdade? `fluxo.md` separado por modo (criar vs refatorar)? Sem duplicação entre SKILL.md e referências?
- Nenhum arquivo órfão (referenciado mas não existe) ou morto (existe mas ninguém aponta)?

## 5. Contexto isolado via sub-agente
- Fluxo descreve lance de `general-purpose` com contexto limpo (só disco real) que monta rascunho e **não** escreve? Agente principal só confirma e escreve?

## 6. Permissão de uso único
- Todo `Write`/`Edit`/nuvem exige prévia + `[S/N]` com `S` explícito naquela vez? Sem "já autorizou antes"?

## 7. Linguagem e genericidade
- PT-BR, frases curtas, voz ativa, genérica para qualquer stack? Sem amarrar a framework específico?

## 8. Templates (se houver)
- `templates/*.md` com placeholders claros e sem lógica duplicada das referências?

## 9. Versionamento
- Bump previsto nos 4 manifests após `S`?

Se algum reprovar, corrigir no rascunho antes da prévia. Todos ok → pronto para pedir permissão.
