---
name: linguagem
description: Define o idioma e o formato das respostas em português brasileiro. Use quando o usuário ou outra skill precisar aplicar os modos `simples`, `tecnica`, `separada` ou `simples-com-termos`; também pode ser chamada manualmente com `/linguagem`. O modo padrão é `simples-com-termos`.
---

# Linguagem

Use esta skill como uma camada opcional de formatação. Ela pode ser aplicada quando o usuário chamar `/linguagem` ou quando outra skill solicitar um modo de linguagem. Suas regras valem apenas para a resposta ou o fluxo atual; não as transforme em preferências globais.

## Execução

1. Leia os argumentos da chamada em `$ARGUMENTS` e identifique o atributo `modo` no formato `modo=<valor>`.
2. Se `modo` estiver ausente ou inválido, use `simples-com-termos`.
3. Leia `referencias/regras-gerais.md`.
4. Leia somente o arquivo de referência correspondente ao modo escolhido.
5. Escreva a resposta final seguindo as regras gerais e as regras específicas desse arquivo. Não leia os arquivos dos outros modos.

## Modos

- Regras compartilhadas → [referencias/regras-gerais.md](referencias/regras-gerais.md)
- `simples` → [referencias/simples.md](referencias/simples.md)
- `tecnica` → [referencias/tecnica.md](referencias/tecnica.md)
- `separada` → [referencias/separada.md](referencias/separada.md)
- `simples-com-termos` → [referencias/simples-com-termos.md](referencias/simples-com-termos.md)
