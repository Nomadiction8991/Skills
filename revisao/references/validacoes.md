# Validações — o que realmente buscar (genérico, qualquer stack) — pontuais que complementam o code-review genérico

Use como checklist **além** do code-review genérico do Claude (reaproveitado em `processo.md`, Parte A, e `regras.md`) e dos 7 eixos. O genérico pega bugs rasos e `AGENTS.md`/`REVIEW.md` compliance; estas 24 validações pegam o que reprova MR na prática e é específico do fluxo de revisão. Para cada item: o que buscar, como verificar, quando reprovar.

## Escopo da revisão

Todas as validações abaixo devem ser aplicadas ao diff do modo selecionado. Busca global, histórico, arquivos vizinhos e testes ajudam a confirmar o contexto, mas não ampliam o escopo. Só gere achado quando o problema for introduzido pelo diff ou estiver diretamente no conteúdo revisado. Se não se aplicar ao stack/projeto, marque "verificado sem achado" em vez de silenciar.

## Donos (quem verifica o quê — cada agent lê só o seu subconjunto)

- **Fiscal** (2 instâncias) — compliance `AGENTS.md`/`REVIEW.md` + #13, #15
- **Batedor** — #1, #2, #3, #6, #16, #17, #18 (diff-only; #17 compara linhas removidas vs adicionadas no próprio diff)
- **Detetive** — #4, #5, #7, #8, #9, #10, #11, #12, #19, #20, #21, #22, #23, #24 + `code-smells.md`
- **Orquestrador** (sem agent) — gate de tamanho/mistura (`revisao-exigente.md` #0) + #14 (rodar a suíte completa)

Briefings e pós-agents em `references/agents.md`. Cobertura total 1–24, sem sobreposição: se um ponto passar, sabe-se quem deixou passar.

## 1. Autorização e escopo do dono do dado
**O que buscar:** qualquer endpoint/ação que recebe identificador de recurso (id, slug, token) e retorna ou altera o recurso.
**Como verificar:** o recurso é filtrado pelo usuário autenticado E pelo escopo do tenant/organização/workspace do contexto? Autenticação não é autorização. Compare com código irmão no mesmo módulo que já valida — se ele valida e este não, é bug. Respeite `REVIEW.md` se ele define "queries devem ser scoping por tenant".
**Reprova quando:** trocar o identificador na URL/request expõe dado de outro usuário/tenant.

## 2. Mesma informação derivada de formas divergentes
**O que buscar:** mesmo dado calculado em dois lugares (ex.: detalhe vs listagem, API vs tela, relatório vs dashboard).
**Como verificar:** mesma fonte? mesmos filtros (ativo/inativo, tenant, soft-delete, ordenação)? mesmo fallback ("Não informado") e formato (moeda, data/hora, timezone, arredondamento)?
**Reprova quando:** duas telas mostram valores diferentes para o mesmo registro sem erro. Sugerir extrair para um único método/serviço/helper.

## 3. Código morto
**O que buscar:** função/método/classe/rota/import/parâmetro/coluna adicionado e não referenciado em nenhum lugar (incluindo templates e frontend), `console.log`/`print`/`debug` esquecido, parâmetro recebido e ignorado, flag inalcançável.
**Como verificar:** busca global no projeto, não só leitura do diff. Sem chamada = morto — apontar como achado para o usuário decidir sobre remoção.

## 4. Indireção desnecessária
**O que buscar:** wrapper que só repassa chamada (`function x(){ return y() }`), função de 1 linha que já existe nativa no projeto.
**Como verificar:** se o corpo é trivial e já existe utilitário/property nativa, apontar para simplificação/uso direto em vez de criar abstração sem necessidade real.

## 5. Duplicação e fonte única
**O que buscar:** constante, query, regra de negócio, texto ou validação copiada que já existe em outro arquivo.
**Como verificar:** procurar onde já existe (constante central, helper compartilhado, evento único). Se já existe, apontar a duplicação e sugerir reutilização. Duplicação hoje vira divergência amanhã.

## 6. Responsabilidade no lugar certo
**O que buscar:** lógica no arquivo/módulo/camada errada.
**Como verificar:** perguntar "essa classe/módulo deveria saber disso?" Se já existe dono (entidade, utilitário de strings, serviço central, camada de domínio), apontar o desvio e sugerir mover para lá. Não espalhar regra que deveria estar concentrada.

## 7. Simplicidade de API e fluxo
**O que buscar:** múltiplas requests parciais onde 1 completa resolve; operação não idempotente que poderia ser; múltiplas flags quando 1 resolve; fluxo com direção fixa usando 2 controles.
**Como verificar:** prefira 1 request com todos os dados, operação idempotente, 1 flag em vez de 2, notificação centralizada. Menos caminho = menos bug.

## 8. Separação de refatoração e feature
**O que buscar:** no mesmo diff/MR, renomeação/movimentação/limpeza misturada com nova regra/funcionalidade.
**Como verificar:** refatoração e feature devem ser commits/MRs separados. Misturar esconde o que deve ser revisado e dificulta revert.

## 9. Padrões do framework/stack vencem preferência
**O que buscar:** código que reinventa o que a linguagem/framework/biblioteca já oferece (ex.: ordenar manualmente quando existe ordenação declarativa, montar query na mão quando existe query builder/scope, criar parser para remontar SQL quando existe propriedade declarativa).
**Como verificar:** usar o recurso nativo do stack. Se introduzir padrão novo, deixar guarda (`Assert`/teste) para não regredir — ex.: projeto com ordenação declarativa `OrderBySQL` com `Assert` contra `ORDER BY` no arquivo de form (padrão antes reinventado com parser). Adapte o exemplo ao stack do diff; não amarre a um framework específico.

## 10. Infra: produção enxuta
**O que buscar:** dependência de teste/dev na imagem/artefato de produção, lógica de teste espalhada em comandos ad-hoc.
**Como verificar:** artefato de produção sem peso de dev; criar imagem/config separada para dev e script centralizado (ex.: `test.sh`) para testes. Teste não infla produção.

## 11. Pipeline e configuração de ambiente
**O que buscar:** pipeline que amarra app ao container de banco, env vars duplicadas/erradas, runner/host hard-coded, falta de flexibilidade para trocar serviço externo.
**Como verificar:** pipeline foca só no app; banco é considerado externo e já em execução; `DB_HOST` vem de env, não hard-coded; não duplicar env que já é requisito do serviço externo (`ex.: DB_PASSWORD` vs `DB_ROOT_PASSWORD` quando o serviço externo já exige um); manter flexibilidade para trocar provedor (ex.: RDS) sem mudar pipeline. Ex.: extraído de MR onde pipeline fixou host/DB — genérico, adapte ao provedor do repo.

## 12. Migração e procedimento
**O que buscar:** mudança de schema/banco, adição de coluna/tabela, alteração de tipo, sem descrever como migrar dados existentes, ordem de deploy ou rollback.
**Como verificar:** todo diff que toca schema deve vir com "Qual o procedimento para migração?" respondido: script, ordem, compatibilidade com dado legado, downtime, rollback. Ex.: MR que adicionou coluna sem script — genérico, aplique ao banco do repo.

## 13. Idioma consistente em nomes e comentários
**O que buscar:** nomes de funções, métodos, variáveis, classes, arquivos que misturam português e inglês no mesmo identificador ou no mesmo módulo (ex.: `repeatOrder`, `getBairro`, `calcularTotalPrice`); comentário de código em idioma diferente da convenção do repo (ex.: comentário em inglês num projeto que comenta em PT-BR).
**Como verificar:** siga a convenção do repo alvo. Ex.: muitos projetos legado neste ecossistema preferem português total (`repetirPedido`, `buscarBairro`, `calcularTotal`) — nesse caso, prefira português total e só use inglês se o arquivo/módulo já for 100% em inglês. Em repo 100% inglês, faça o inverso: prefira inglês total. Nunca misturar os dois no mesmo nome (`getBairro`, `calcularTotalPrice` reprova). Comentário segue a mesma regra: se o repo comenta em PT-BR, comentário novo em inglês reprova como `menor` (ex.: extraído de MR onde migration veio com comentário em inglês). Se o repo for misto por legado, aponte como `menor` e sugira padronizar no arquivo tocado. Verifique `AGENTS.md`/`REVIEW.md` se definem idioma.

## 14. Sempre rodar todos os testes
**O que buscar:** diff que não foi validado com a suíte completa; testes que passam só no modo isolado mas quebram no conjunto.
**Como verificar:** identificar como rodar os testes no repo em que está — procurar em ordem: `Makefile` (`make test`, `make tests`, `make check`), `README.md`/`AGENTS.md`/`CONTRIBUTING.md`, `package.json` (`scripts.test`), `composer.json`, `pyproject.toml`/`tox`, `cargo test`, `./test.sh`, `docker exec` etc. Leia o arquivo e extraia o comando exato. Rodar a suíte completa (`make test` ou equivalente) e verificar se todos passam. Se falhar, só reporte a falha quando ela for causada pelo conteúdo do diff; falhas pré-existentes ou sem relação ficam fora do relatório. Se não houver como rodar (sem Docker, sem env), declarar "testes não executados — motivo" em vez de silenciar.

## 15. Comentários só se necessário, curtos e simples
**O que buscar:** comentário em cima de função/método ou dentro do código que não é necessário — função já se explica pelo nome e corpo, comentário repete o óbvio, ocupa muitas linhas ou usa jargão.
**Como verificar:** comente **apenas** se a função for muito complexa para entender só pelo nome/código ou se não descreve o que faz e o desenvolvedor ficaria sem rumo. Se comentar, o comentário de linha deve ser **pequeno, em linguagem simples e bem resumido** (1–2 linhas, diz o porquê/regra de negócio, não o o quê). Comentário que só repete `// soma 1 ao total` é ruído — remover. Comentário grande deve virar nome melhor ou extração de método.
**Reprova quando:** comentário desnecessário, longo, que repete o código, ou que ocupa muitas linhas sem ajudar quem vai manter. Sugerir remover ou resumir em 1 linha simples.

## Como usar
Passe por 1–24 em todo diff, em qualquer projeto. Se não se aplica, declare "verificado sem achado" — silêncio não é verificação. Severidade e ordenação conforme `regras.md` step 8. Use `REVIEW.md` para calibrar severidade se o repo definir o que é `bloqueia` nele.

## 16. Incerteza nunca destrói dado
**O que buscar:** falha de rede/timeout/resposta ilegível tratada como falha definitiva com apagamento, rollback destrutivo ou consumo de sequência; retry que gera "duplicidade" tratada como erro novo.
**Como verificar:** pergunte "e se o pedido chegou no serviço externo mas a resposta se perdeu?". Falha de transporte (exceção, timeout, resposta vazia, `lastResponse` ausente) = estado indeterminado: nunca apaga, nunca consome sequência, nunca marca como rejeitado. Duplicidade/retentativa tem caminho próprio (consulta, conciliação, reuso do mesmo número), não cai no mesmo `remove/apaga` da rejeição. Ex.: extraído de MR onde timeout sem resposta apagava a carta e o retry caía em "duplicidade" que apagava de novo — genérico, aplique a qualquer integração externa.
**Reprova quando:** um caminho de erro apaga registro que pode ter sido efetivado fora; retry legítimo vira erro tratado como rejeição.

## 17. Mensagem de erro preserva a causa
**O que buscar:** flash/toast/log/mensagem que antes mostrava código + motivo (`cstat - xmotivo`, código HTTP, regra violada) e agora mostra genérico ("tente mais tarde"); causa visível só no log.
**Como verificar:** compare a mensagem antes/depois no diff. A mensagem nova deve manter o motivo acionável que a antiga tinha (código + motivo legível). Genérico só vale se acompanhado do motivo específico. Usuário que não sabe o motivo reenvia o que será rejeitado de novo; suporte não pode depender só de log.
**Reprova quando:** o diff remove da tela uma informação de diagnóstico que existia antes sem substituto equivalente.

## 18. Toda exceção tem dono e caminho visível
**O que buscar:** `throw`/`raise`/`relançar` sem `catch`/`rescue` no caminho do chamador; falha de DB/transação que vira 500 silencioso; frontend que usa `fetch` com `redirect: manual` (ou equivalente) e engole o erro sem mensagem.
**Como verificar:** a partir de cada `throw`, suba a pilha de chamadores no diff: quem captura? O que o usuário vê? Se ninguém captura no fluxo da tela/API, é 500 ou silêncio. Falha ao desfazer/limpar (ex.: apagar rascunho após rejeição) deve logar e seguir com mensagem de erro, nunca relançar para a tela. Ex.: extraído de MR onde `throw $e` na remoção pós-rejeição gerava 500 e o JS voltava ao index sem mensagem, com rascunho órfão e sequência consumida.
**Reprova quando:** exceção escapa para a tela sem mensagem; erro de limpeza esconde o erro original.

## 19. Todo estado tem saída
**O que buscar:** status novo que nenhum código lê depois (ex.: `INDETERMINADO`, `PENDENTE`, `AGUARDANDO`); registro que fica para sempre sem consulta, retry, expiração ou limpeza; lacuna de sequência e registro órfão na grade.
**Como verificar:** para cada status novo, liste quem transiciona para fora dele (consulta externa, nova tentativa, job, tela). Se ninguém transiciona, é beco sem saída. Verifique também: próximo envio reusa a mesma sequência ou pula (`seq+1`)? Grade/lista mostra o órfão sem ação? Ex.: extraído de MR onde carta `INDETERMINADO` nunca obtinha XML nem liberava nova tentativa — o oposto do que o MR prometia.
**Reprova quando:** existe caminho que cria registro/estado sem caminho que resolve, concilia ou remove.

## 20. Persistência enxuta, sem dado repetido
**O que buscar:** coluna nova que guarda dado derivável do que já existe; payload JSON que embute blob já guardado em outra coluna (XML + PDF em base64 dentro de `resposta_json` quando já há `xml_evento` e `pdf`); mesma entidade gravada duas vezes no mesmo fluxo; PDF guardado quando pode ser gerado sob demanda do XML.
**Como verificar:** para cada coluna nova, pergunte "dá para derivar do que já tenho na hora de ler?". Se sim, não persista — gere sob demanda. Para cada `encode(resposta)`/`toJson`, abra o conteúdo: contém blob que já tem coluna própria? Se sim, salve só metadados (código, motivo, protocolo), sem XML/PDF. Conte os `save` no fluxo: dois `save` da mesma entidade no mesmo request = escrita dupla — fundir. Considere custo multi-tenant (ex.: banco por cliente `bd_<cnpj>`): blob triplicado multiplica em todos os clientes.
**Reprova quando:** o mesmo bytes mora em 2+ lugares; entidade é gravada 2x sem necessidade; coluna guarda o que é gerável na leitura.

## 21. Contrato externo e legado removido
**O que buscar:** valor de tela (label humano, ex.: `Observações`, `Placa Veículo`) indo cru para XML/API externa que espera código de leiaute (ex.: grupo `compl`, campo `xObs`); função/método legado removido (`json()`, `enviaX`, mapper) sem verificar quem consumia o mapeamento que ele fazia.
**Como verificar:** liste tudo que foi removido no diff e quem chamava cada item; o mapeamento que o removido fazia precisa ter vindo para o código novo, ou o novo precisa provar que não precisa dele (teste em homologação, doc do leiaute). Valor que vai para serviço externo/fisco nunca usa texto de tela — usa código do contrato. Marque como `importante` com pedido explícito de teste em homologação quando não der para provar pelo código.
**Reprova quando:** remoção de caminho antigo leva junto um mapeamento/validação que o novo não reproduz; texto de UI vaza para contrato externo.

## 22. Config central vence cópia paralela; retorno nunca é lixo
**O que buscar:** montagem de config/certificado/headers na mão repetindo o que já existe em builder/configurador central (ex.: `Configurador::getConfig` usado pelo emissor principal); retorno de `carregaX` descartado enquanto outra função lê o mesmo arquivo/disco de novo; versão/scheme/UF/proxy hard-coded num lugar e central noutro.
**Como verificar:** procure o configurador/builder central do módulo e compare campo a campo (versão, schemes, UF, proxy, timeout). Se o novo monta paralelo, aponte divergência futura e sugira reaproveitar o central. Se o retorno de uma leitura é ignorado e o mesmo arquivo é lido de novo abaixo, aponte leitura dupla. Ex.: extraído de MR onde config de CC-e repetia a da emissão e `carregaCertificadoA1` era descartado enquanto `criaTools` relia o `.pfx`.
**Reprova quando:** dois lugares montam a mesma config; mesmo IO feito 2x; retorno descartado sem motivo.

## 23. Ordenação e filtro obedecem ao framework
**O que buscar:** `orderBy` na query + `DataProvider`/`sort` com `defaultOrder`; ordenação manual quando o framework aplica a dele depois (ex.: `addOrderBy` sobrescreve); filtro que não valida tipo/limite.
**Como verificar:** leia como o componente de grade do stack compõe a ordenação (quem ganha: `orderBy` da query ou `sort` do provider?). Se o framework aplica depois, remova o `orderBy` e deixe só o `defaultOrder` do `sort`. Valide filtros de query string (tipo, dígito, tamanho, data válida) antes de usar. Ex.: extraído de MR onde `orderBy` fixo anulava o clique nas colunas no Yii 2.
**Reprova quando:** clique em coluna não muda a ordem; filtro aceita valor que quebra a query.

## 24. Nova rota tem permissão; sequência é retry-safe
**O que buscar:** action/rota/endpoint novo sem registro de permissão (ex.: RBAC, tabela `Menu`/`MenuPerfil`, policy, `accessRules` editado na mão em vez da fonte central); operação com número de sequência externo (ex.: `nSeqEvento`, versão, nonce) que consome número em falha e não trata reenvio.
**Como verificar:** para cada rota nova, mostre onde a permissão foi cadastrada; edição manual de `behaviors()`/`rules` que duplica a fonte central reprova — permissão nasce na fonte (RBAC/policy), não no controller. Para sequência: falha consome número? Retry reusa ou pula? Duplicidade tem tratamento próprio? Se falhar antes do aceite, o número deve ficar reusável; se aceitou, nunca reenviar o mesmo número como novo.
**Reprova quando:** rota nova acessível sem permissão explícita; falha consome sequência sem caminho de reuso; duplicidade cai em fluxo de rejeição.
