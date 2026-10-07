---
name: linguagem
description: "Define o idioma e o formato das respostas em português brasileiro. Use quando o usuário ou outra skill precisar aplicar os modos `simples`, `tecnica`, `separada` ou `simples-com-termos`; também pode ser chamada manualmente com `/linguagem`. O modo padrão é `simples-com-termos`. Do NOT use for coding tasks — response language and formatting only."
model: haiku
effort: low
argument-hint: "[modo]"
allowed-tools: Read
---

# Linguagem

Use esta skill como uma camada opcional de formatação. Ela pode ser aplicada quando o usuário chamar `/linguagem` ou quando outra skill solicitar um modo de linguagem. Suas regras valem apenas para a resposta ou o fluxo atual; não as transforme em preferências globais.

## Execução

1. Leia os argumentos da chamada em `$ARGUMENTS` e identifique o atributo `modo` no formato `modo=<valor>`.
2. Se `modo` estiver ausente ou inválido, use `simples-com-termos`.
3. Leia `references/regras-gerais.md`.
4. Leia somente o arquivo de referência correspondente ao modo escolhido.
5. Escreva a resposta final seguindo as regras gerais e as regras específicas desse arquivo. Não leia os arquivos dos outros modos.

## Modos

- Regras compartilhadas → [references/regras-gerais.md](references/regras-gerais.md)
- `simples` → [references/simples.md](references/simples.md)
- `tecnica` → [references/tecnica.md](references/tecnica.md)
- `separada` → [references/separada.md](references/separada.md)
- `simples-com-termos` → [references/simples-com-termos.md](references/simples-com-termos.md)

## Exemplo

`/linguagem modo=simples-com-termos` aplica esse modo à resposta.

## Troubleshooting

- Modo inválido: usa o padrão `simples-com-termos`.
- Conflito com pedido explícito do usuário: o pedido do usuário vence.
