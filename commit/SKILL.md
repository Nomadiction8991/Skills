---
name: commit
description: Cria commits bem formatados seguindo o padrão conventional commit.
model: haiku
---

# Commit Git Inteligente

Cria commit bem formatado: $ARGUMENTS

## Estado Atual do Repositório

- Status Git: !`git status --porcelain`
- Branch atual: !`git branch --show-current`
- Alterações em staging: !`git diff --cached --stat`
- Alterações não em staging: !`git diff --stat`
- Commits recentes: !`git log --oneline -5`

## O Que Este Comando Faz

1. Verifica quais arquivos estão em staging com `git status`
2. Se 0 arquivos estiverem em staging, adiciona automaticamente todos os arquivos modificados e novos com `git add`
3. Executa um `git diff` para entender quais alterações estão sendo commitadas
4. Analisa o diff para determinar se há múltiplas alterações lógicas distintas
5. Se múltiplas alterações distintas forem detectadas, sugere dividir o commit em vários commits menores
6. Para cada commit (ou o único commit se não for dividido), cria uma mensagem de commit usando o formato conventional commit

## Melhores Práticas para Commits

- Siga a especificação Conventional Commits descrita abaixo.

# Conventional Commits 1.0.0

## Resumo

A especificação Conventional Commits é uma convenção leve sobre mensagens de commit. Ela fornece um conjunto fácil de regras para criar um histórico de commits explícito, facilitando a escrita de ferramentas automatizadas. Esta convenção se alinha com [SemVer](http://semver.org), descrevendo as funcionalidades, correções e mudanças significativas nas mensagens de commit.

A mensagem de commit deve ser estruturada da seguinte forma:

```
<tipo>[escopo opcional]: <descrição>

[corpo opcional]

[rodapé(s) opcional(is)]
```

O commit contém os seguintes elementos estruturais para comunicar a intenção aos consumidores da sua biblioteca:

1. **fix:** um commit do _tipo_ `fix` corrige um bug na sua base de código (correlaciona-se com [`PATCH`](http://semver.org/#summary) no Versionamento Semântico).
2. **feat:** um commit do _tipo_ `feat` introduz uma nova funcionalidade na base de código (correlaciona-se com [`MINOR`](http://semver.org/#summary) no Versionamento Semântico).
3. **BREAKING CHANGE:** um commit que possui um rodapé `BREAKING CHANGE:`, ou acrescenta um `'!'` após o tipo/escopo, introduz uma mudança significativa de API (correlacionando-se com [`MAJOR`](http://semver.org/#summary) no Versionamento Semântico). Uma BREAKING CHANGE pode fazer parte de commits de qualquer _tipo_.
4. _Tipos_ diferentes de `fix:` e `feat:` são permitidos, por exemplo [@commitlint/config-conventional](https://github.com/conventional-changelog/commitlint/tree/master/%40commitlint/config-conventional) (baseado na [convenção Angular](https://github.com/angular/angular/blob/22b96b9/CONTRIBUTING.md#-commit-message-guidelines)) recomenda `build:`, `chore:`, `ci:`, `docs:`, `style:`, `refactor:`, `perf:`, `test:`, entre outros.
5. _Rodapés_ diferentes de `BREAKING CHANGE: <descrição>` podem ser fornecidos e seguem uma convenção similar ao [formato git trailer](https://git-scm.com/docs/git-interpret-trailers).

Tipos adicionais não são exigidos pela especificação Conventional Commits e não têm efeito implícito no Versionamento Semântico (a menos que incluam uma BREAKING CHANGE). Um escopo pode ser fornecido ao tipo do commit para fornecer informações contextuais adicionais e é inserido entre parênteses, ex.: `feat(parser): add ability to parse arrays`.

## Exemplos

### Mensagem de commit com descrição e rodapé de breaking change

```
feat: permitir que o objeto de configuração estenda outras configurações

BREAKING CHANGE: a chave `extends` no arquivo de configuração agora é usada para estender outros arquivos de configuração
```

### Mensagem de commit com `'!'` para destacar uma breaking change

```
feat'!': enviar um e-mail ao cliente quando um produto for enviado
```

### Mensagem de commit com escopo e `'!'` para destacar uma breaking change

```
feat(api)'!': enviar um e-mail ao cliente quando um produto for enviado
```

### Mensagem de commit com `'!'` e rodapé BREAKING CHANGE

```
chore'!': remover suporte ao Node 6

BREAKING CHANGE: usar recursos JavaScript não disponíveis no Node 6.
```

### Mensagem de commit sem corpo

```
docs: corrigir ortografia do CHANGELOG
```

### Mensagem de commit com escopo

```
feat(lang): adicionar idioma polonês
```

### Mensagem de commit com corpo de vários parágrafos e múltiplos rodapés

```
fix: evitar concorrência de requisições

Introduzir um ID de requisição e uma referência à requisição mais recente.
Descartar respostas recebidas que não sejam da requisição mais recente.

Remover timeouts que eram usados para mitigar o problema de concorrência
mas agora estão obsoletos.

Revisado-por: Z
Refs: #123
```

## Especificação

As palavras-chave "DEVE", "NÃO DEVE", "OBRIGATÓRIO", "DEVERÁ", "NÃO DEVERÁ", "RECOMENDADO", "PODE" e "OPCIONAL" neste documento devem ser interpretadas conforme descrito na [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).

1. Commits DEVEM ser prefixados com um tipo, que consiste em um substantivo, `feat`, `fix`, etc., seguido pelo escopo OPCIONAL, `'!'` OPCIONAL, e dois-pontos e espaço OBRIGATÓRIOS.
2. O tipo `feat` DEVE ser usado quando um commit adiciona uma nova funcionalidade à sua aplicação ou biblioteca.
3. O tipo `fix` DEVE ser usado quando um commit representa uma correção de bug para sua aplicação.
4. Um escopo PODE ser fornecido após um tipo. Um escopo DEVE consistir em um substantivo descrevendo uma seção da base de código, cercado por parênteses, ex.: `fix(parser):`
5. Uma descrição DEVE seguir imediatamente os dois-pontos e espaço após o prefixo tipo/escopo. A descrição é um resumo curto das alterações no código, ex.: _fix: problema de parsing de array quando múltiplos espaços estavam contidos na string_.
6. Um corpo de commit mais longo PODE ser fornecido após a descrição curta, fornecendo informações contextuais adicionais sobre as alterações no código. O corpo DEVE começar com uma linha em branco após a descrição.
7. Um corpo de commit é de forma livre e PODE consistir em qualquer número de parágrafos separados por nova linha.
8. Um ou mais rodapés PODEM ser fornecidos uma linha em branco após o corpo. Cada rodapé DEVE consistir em um token de palavra, seguido por um separador `:<space>` ou `<space>#`, seguido por um valor string (isto é inspirado na [convenção git trailer](https://git-scm.com/docs/git-interpret-trailers)).
9. O token de um rodapé DEVE usar `-` no lugar de espaços em branco, ex.: `Acked-by` (isto ajuda a diferenciar a seção de rodapé de um corpo com vários parágrafos). Uma exceção é feita para `BREAKING CHANGE`, que TAMBÉM PODE ser usada como token.
10. O valor de um rodapé PODE conter espaços e novas linhas, e o parsing DEVE terminar quando o próximo par token/separador de rodapé válido for observado.
11. Mudanças significativas (breaking changes) DEVEM ser indicadas no prefixo tipo/escopo de um commit, ou como uma entrada no rodapé.
12. Se incluída como rodapé, uma breaking change DEVE consistir no texto em maiúsculas BREAKING CHANGE, seguido por dois-pontos, espaço e descrição, ex.: _BREAKING CHANGE: variáveis de ambiente agora têm precedência sobre arquivos de configuração_.
13. Se incluída no prefixo tipo/escopo, uma breaking change DEVE ser indicada por um `'!'` imediatamente antes dos `:`. Se `'!'` for usado, `BREAKING CHANGE:` PODE ser omitido da seção de rodapé, e a descrição do commit DEVERÁ ser usada para descrever a breaking change.
14. Tipos diferentes de `feat` e `fix` PODEM ser usados em suas mensagens de commit, ex.: _docs: atualizar documentação de referência._
15. As unidades de informação que compõem o Conventional Commits NÃO DEVEM ser tratadas como sensíveis a maiúsculas/minúsculas pelos implementadores, com exceção de BREAKING CHANGE que DEVE ser em maiúsculas.
16. BREAKING-CHANGE DEVE ser sinônimo de BREAKING CHANGE quando usado como token em um rodapé.

## Por Que Usar Conventional Commits

- Gerar CHANGELOGs automaticamente.
- Determinar automaticamente um bump de versão semântica (baseado nos tipos de commits realizados).
- Comunicar a natureza das alterações para colegas de equipe, o público e outras partes interessadas.
- Disparar processos de build e publicação.
- Facilitar a contribuição de pessoas para seus projetos, permitindo que explorem um histórico de commits mais estruturado.

## FAQ

### Como devo lidar com mensagens de commit na fase inicial de desenvolvimento?

Recomendamos que você prossiga como se já tivesse lançado o produto. Normalmente _alguém_, mesmo que sejam seus colegas desenvolvedores, está usando seu software. Eles vão querer saber o que foi corrigido, o que quebrou, etc.

### Os tipos no título do commit devem ser em maiúsculas ou minúsculas?

Qualquer capitalização pode ser usada, mas é melhor ser consistente.

### O que faço se o commit se enquadrar em mais de um tipo de commit?

Volte e faça múltiplos commits sempre que possível. Parte do benefício do Conventional Commits é sua capacidade de nos levar a fazer commits e PRs mais organizados.

### Isso não desencoraja desenvolvimento rápido e iteração rápida?

Desencoraja a pressa de forma desorganizada. Ajuda você a conseguir avançar rapidamente a longo prazo em múltiplos projetos com colaboradores variados.

### O Conventional Commits pode levar os desenvolvedores a limitar o tipo de commits que fazem porque pensarão nos tipos fornecidos?

O Conventional Commits nos incentiva a fazer mais de certos tipos de commits, como correções. Além disso, a flexibilidade do Conventional Commits permite que sua equipe crie seus próprios tipos e mude esses tipos ao longo do tempo.

### Como isso se relaciona com o SemVer?

Commits do tipo `fix` devem ser traduzidos para lançamentos `PATCH`. Commits do tipo `feat` devem ser traduzidos para lançamentos `MINOR`. Commits com `BREAKING CHANGE`, independentemente do tipo, devem ser traduzidos para lançamentos `MAJOR`.

### Como devo versionar minhas extensões da Especificação Conventional Commits, ex.: `@jameswomack/conventional-commit-spec`?

Recomendamos usar SemVer para lançar suas próprias extensões desta especificação (e encorajamos você a fazer essas extensões'!')

### O que faço se eu acidentalmente usar o tipo de commit errado?

#### Quando você usou um tipo que está na especificação mas não é o tipo correto, ex.: `fix` em vez de `feat`

Antes de mesclar ou lançar o erro, recomendamos usar `git rebase -i` para editar o histórico de commits. Após o lançamento, a limpeza será diferente dependendo das ferramentas e processos que você utiliza.

#### Quando você usou um tipo _não_ da especificação, ex.: `feet` em vez de `feat`

No pior cenário, não é o fim do mundo se um commit for feito que não atenda à especificação Conventional Commits. Simplesmente significa que esse commit será ignorado por ferramentas baseadas na especificação.

### Todos os meus colaboradores precisam usar a especificação Conventional Commits?

Não'!' Se você usa um fluxo de trabalho baseado em squash no Git, os mantenedores principais podem limpar as mensagens de commit conforme são mescladas, sem adicionar carga de trabalho aos contribuidores casuais. Um fluxo de trabalho comum para isso é fazer com que seu sistema git faça squash automaticamente dos commits de um pull request e apresente um formulário para o mantenedor principal inserir a mensagem de commit adequada para a mesclagem.

### Como o Conventional Commits lida com commits de reversão?

Reverter código pode ser complicado: você está revertendo múltiplos commits? Se você reverter uma funcionalidade, o próximo lançamento deve ser um patch?

O Conventional Commits não faz um esforço explícito para definir o comportamento de reversão. Em vez disso, deixamos para os criadores de ferramentas usarem a flexibilidade de _tipos_ e _rodapés_ para desenvolver sua lógica para lidar com reversões.

Uma recomendação é usar o tipo `revert` e um rodapé que referencie os SHAs dos commits que estão sendo revertidos:

```
revert: nunca mais falemos do incidente do macarrão

Refs: 676104e, a215868
```

### Atribuição de Autoria de Código Assistido por IA

Ao usar ferramentas de IA para gerar código, pode ser benéfico manter transparência sobre a autoria para fins de responsabilidade, revisão de código e auditoria. Isso pode ser feito facilmente usando Git trailers que anexam metadados estruturados ao final das mensagens de commit.

Isso pode ser feito acrescentando um ou mais trailers personalizados na mensagem de commit, como:

```
Assistant-model: Claude Code
```

Como a maioria das ferramentas Git espera que os trailers `Co-authored-by` sejam formatados como endereços de e-mail, você deve usar uma chave de trailer diferente para evitar confusão e distinguir autoria de assistência.

Os trailers podem ser adicionados manualmente ao final de uma mensagem de commit, ou usando o comando `git commit` com a opção `--trailer`:

```
git commit --message "Implementar funcionalidade" --trailer "Assistant-model: Claude Code"
```

Os trailers podem ser exibidos usando a opção [pretty formats](https://git-scm.com/docs/pretty-formats#Documentation/pretty-formats.txt-trailersoptions) do comando `git log`. Por exemplo, para um histórico formatado mostrando o hash, nome do autor e modelos de assistente usados em cada commit:

```
git log --color --pretty=format:"%C(yellow)%h%C(reset) %C(blue)%an%C(reset) [%C(magenta)%(trailers:key=Assistant-model,valueonly=true,separator=%x2C)%C(reset)] %s%C(bold cyan)%d%C(reset)"
```

```
2100e6c Autor [Claude Code] Commit de teste 4 (HEAD -> work-item-8)
7120221 Autor [Claude Code] Commit de teste 3
ea03d91 Autor [] Commit de teste 2
f93fd8e Autor [Claude Code] Commit de teste 1
dde0159 Claude Code [] Item de trabalho de teste (#7) (origin/main, origin/HEAD)
```

## Notas Importantes

- Por padrão, as verificações de pre-commit (definidas em `prek.toml`) serão executadas para garantir a qualidade do código
    - IMPORTANTE: NÃO PULE as verificações de pre-commit
- SEMPRE atribua autoria de código assistido por IA
- Se arquivos específicos já estiverem em staging, o comando commitá apenas esses arquivos
- Se nenhum arquivo estiver em staging, ele adicionará automaticamente todos os arquivos modificados e novos
- A mensagem de commit será construída com base nas alterações detectadas
- Antes de commit, o comando revisará o diff para identificar se múltiplos commits seriam mais apropriados
- Se sugerir múltiplos commits, ele ajudará você a colocar em staging e commitar as alterações separadamente
- Sempre revise o diff do commit para garantir que a mensagem corresponda às alterações
