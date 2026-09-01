# Criar Skill — Ajuda

Cria ou refatora skills em `plugins/fluxo-trabalho/skills/<nome>/` nos padrões do marketplace.

## Uso

- `/criar-skill minha-skill` — cria nova skill chamada `minha-skill`
- `/criar-skill refatorar revisao` — refatora a skill `revisao` para os padrões atuais
- `/criar-skill` + descreva livremente ("skill que revisa SQL...") — entrevista curta para fechar nome e escopo

## O que a skill faz

- Lê os padrões reais do repo (`commit`, `revisao`, `chamado-tomticket`) em sub-agente com contexto limpo
- Monta `SKILL.md` enxuto (só roteamento + apontamentos) + `references/*.md` modulares + `templates/*.md`
- Valida com `references/checklist.md` antes de escrever
- Mostra prévia (árvore + SKILL.md) e pede `[S]/[N]` — permissão de uso único, sem "já autorizou antes"

## Exemplo de descrição boa

"Preciso de uma skill que valide SQL antes de commitar — deve checar sintaxe, semântica e estilo, com modo commit (não commitado) e modo mr (branch vs main), linguagem simples, agents com contexto limpo"

## Padrões que a skill garante

SKILL.md enxuto, referências modulares por args, contexto isolado via `general-purpose`, permissão explícita, linguagem genérica, bump de versão nos 4 manifests.
