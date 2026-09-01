# Validações — o que realmente buscar (genérico, qualquer stack) — pontuais que complementam o code-review genérico

Use como checklist **além** do code-review genérico do Claude (reaproveitado em `processo.md#0`) e dos 7 eixos. O genérico pega bugs rasos e CLAUDE.md compliance; estas 14 validações pegam o que reprova MR na prática e é específico do seu fluxo. Para cada item: o que buscar, como verificar, quando reprovar.

## 1. Autorização e escopo do dono do dado
**O que buscar:** qualquer endpoint/ação que recebe identificador de recurso (id, slug, token) e retorna ou altera o recurso.
**Como verificar:** o recurso é filtrado pelo usuário autenticado E pelo escopo do tenant/organização/workspace do contexto? Autenticação não é autorização. Compare com código irmão no mesmo módulo que já valida — se ele valida e este não, é bug.
**Reprova quando:** trocar o identificador na URL/request expõe dado de outro usuário/tenant.

## 2. Mesma informação derivada de formas divergentes
**O que buscar:** mesmo dado calculado em dois lugares (ex.: detalhe vs listagem, API vs tela, relatório vs dashboard).
**Como verificar:** mesma fonte? mesmos filtros (ativo/inativo, tenant, soft-delete, ordenação)? mesmo fallback ("Não informado") e formato (moeda, data/hora, timezone, arredondamento)?
**Reprova quando:** duas telas mostram valores diferentes para o mesmo registro sem erro. Sugerir extrair para um único método/serviço/helper.

## 3. Código morto
**O que buscar:** função/método/classe/rota/import/parâmetro/coluna adicionado e não referenciado em nenhum lugar (incluindo templates e frontend), `console.log`/`print`/`debug` esquecido, parâmetro recebido e ignorado, flag inalcançável.
**Como verificar:** busca global no projeto, não só leitura do diff. Sem chamada = morto — remover.

## 4. Indireção desnecessária
**O que buscar:** wrapper que só repassa chamada (`function x(){ return y() }`), função de 1 linha que já existe nativa no projeto.
**Como verificar:** se o corpo é trivial e já existe utilitário/property nativa, chamar direto. Evitar abstração sem necessidade real.

## 5. Duplicação e fonte única
**O que buscar:** constante, query, regra de negócio, texto ou validação copiada que já existe em outro arquivo.
**Como verificar:** procurar onde já existe (constante central, helper compartilhado, evento único). Se já existe, reutilizar. Duplicação hoje vira divergência amanhã.

## 6. Responsabilidade no lugar certo
**O que buscar:** lógica no arquivo/módulo/camada errada.
**Como verificar:** perguntar "essa classe/módulo deveria saber disso?" Se já existe dono (entidade, utilitário de strings, serviço central, camada de domínio), mover para lá. Não espalhar regra que deveria estar concentrada.

## 7. Simplicidade de API e fluxo
**O que buscar:** múltiplas requests parciais onde 1 completa resolve; operação não idempotente que poderia ser; múltiplas flags quando 1 resolve; fluxo com direção fixa usando 2 controles.
**Como verificar:** prefira 1 request com todos os dados, operação idempotente, 1 flag em vez de 2, notificação centralizada. Menos caminho = menos bug.

## 8. Separação de refatoração e feature
**O que buscar:** no mesmo diff/MR, renomeação/movimentação/limpeza misturada com nova regra/funcionalidade.
**Como verificar:** refatoração e feature devem ser commits/MRs separados. Misturar esconde o que deve ser revisado e dificulta revert.

## 9. Padrões do framework/stack vencem preferência
**O que buscar:** código que reinventa o que a linguagem/framework/biblioteca já oferece (ex.: ordenar manualmente quando existe ordenação declarativa, montar query na mão quando existe query builder/scope, criar mini parser de SQL para remontar SELECT quando existe propriedade declarativa como `OrderBySQL`).
**Como verificar:** usar o recurso nativo do stack. Se introduzir padrão novo, deixar guarda (`Assert`/teste) para não regredir — como em `ello!1018` onde a ordem passou a ser via `OrderBySQL` com `Assert` contra `ORDER BY` no DFM.

## 10. Infra: produção enxuta
**O que buscar:** dependência de teste/dev na imagem/artefato de produção, lógica de teste espalhada em comandos ad-hoc.
**Como verificar:** artefato de produção sem peso de dev; criar imagem/config separada para dev e script centralizado (ex.: `test.sh`) para testes. Teste não infla produção.

## 11. Pipeline e configuração de ambiente
**O que buscar:** pipeline que amarra app ao container de banco, env vars duplicadas/erradas (`DB_PASSWORD` vs `DB_ROOT_PASSWORD`), runner/host hard-coded, falta de flexibilidade para trocar serviço externo (ex.: RDS).
**Como verificar:** pipeline foca só no app; banco é considerado externo e já em execução; `DB_HOST` vem de env, não hard-coded; não duplicar env que já é requisito do serviço externo; manter flexibilidade para trocar provedor sem mudar pipeline. Extraído de MRs fechados pelo Clayton (ex.: `ello-delivery!65`).

## 12. Migração e procedimento
**O que buscar:** mudança de schema/banco, adição de coluna/tabela, alteração de tipo, sem descrever como migrar dados existentes, ordem de deploy ou rollback.
**Como verificar:** todo diff que toca schema deve vir com "Qual o procedimento para migração?" respondido: script, ordem, compatibilidade com dado legado, downtime, rollback. Extraído de MR fechado `ello-backup!18`.

## 13. Idioma consistente nos nomes — preferência por português total
**O que buscar:** nomes de funções, métodos, variáveis, classes, arquivos que misturam português e inglês no mesmo identificador ou no mesmo módulo (ex.: `repeatOrder`, `getBairro`, `calcularTotalPrice`). Preferência do projeto é manter nomes **totalmente em português**.
**Como verificar:** ver como o sistema foi se desenvolvendo — a maioria dos projetos aqui usa português; portanto, **prefira sempre português total** (`repetirPedido`, `buscarBairro`, `calcularTotal`). Só use inglês se o arquivo/módulo já for 100% em inglês e o padrão do repo exigir. Nunca misturar os dois no mesmo nome (`getBairro`, `calcularTotalPrice` reprova). Se o projeto já é misto por legado, apontar como `menor` e sugerir padronizar para português total no arquivo tocado.

## 14. Sempre rodar todos os testes
**O que buscar:** diff que não foi validado com a suíte completa; testes que passam só no modo isolado mas quebram no conjunto.
**Como verificar:** identificar como rodar os testes no repo em que está — procurar em ordem: `Makefile` (`make test`, `make tests`, `make check`), `README.md`/`AGENTS.md`/`CONTRIBUTING.md`, `package.json` (`scripts.test`), `composer.json`, `pyproject.toml`/`Makefile`/`tox`, `cargo test`, `./test.sh`, `docker exec` etc. Ler o arquivo e extrair o comando exato. Rodar a suíte completa (`make test` ou equivalente) e verificar se todos passam. Se falhar, cada falha é um achado adicional para correção — reportar quais testes quebraram e por quê. Se não houver como rodar (sem Docker, sem env), declarar "testes não executados — motivo" em vez de silenciar.

## 15. Comentários só se necessário, curtos e simples
**O que buscar:** comentário em cima de função/método ou dentro do código que não é necessário — função já se explica pelo nome e corpo, comentário repete o óbvio, ocupa muitas linhas ou usa jargão.
**Como verificar:** comente **apenas** se a função for muito complexa para entender só pelo nome/código ou se não descreve o que faz e o desenvolvedor ficaria sem rumo. Se comentar, o comentário de linha deve ser **pequeno, em linguagem simples e bem resumido** (1–2 linhas, diz o porquê/regra de negócio, não o o quê). Comentário que só repete `// soma 1 ao total` é ruído — remover. Comentário grande deve virar nome melhor ou extração de método.
**Reprova quando:** comentário desnecessário, longo, que repete o código, ou que ocupa muitas linhas sem ajudar quem vai manter. Sugerir remover ou resumir em 1 linha simples.

## Como usar
Passe por 1–15 em todo diff, em qualquer projeto. Se não se aplica, declare "verificado sem achado" — silêncio não é verificação. Ordene achados por severidade: `bloqueia` (dado exposto/perda/quebra) > `importante` (bug visível) > `menor` (limpeza).
