# REVIEW.md — Perfil Clayton++ (revisor mais chato que o Clayton)

Objetivo: se passar aqui, passa no `@claytonaalves` sem comentário. Vale para Ello Delivery (Laravel), Aragorn (MDF-e) e Ello Retaguarda (Delphi).

## 0. Gates bloqueantes (falhou 1 = não abre MR)

1. **Tamanho:** MR >300 linhas ou >10 arquivos ou >1 responsabilidade = fatiar. Nenhum commit >200 linhas sem justificativa. Nenhum MR >1000 linhas nunca.
2. **1 commit = 1 conquista completa:** compila + teste verde sozinho, reversível sozinho. Ordem cronológica não importa.
3. **Refactor separado de feature:** `chore:`/`refactor:` em MR própria. `feat:`/`fix:` só com mínimo do ticket.
4. **Sem dev-tool no código prod:** sem rota `tunnel.*`, sem `formatHostUsing`, sem middleware só pra dev, sem plugin Vite pra `hot`, sem `boot.sh` multi-processo, sem Dockerfile prod com pacote de teste, sem `compose exec` quebrado em worktree.
5. **Sem arquivo local/gerado:** sem `.ignore`, sem ajuste em `.gitignore` pra agente, sem `api-docs.json` gerado, sem `console.log`/`TODO`/`credencial`.
6. **Testes verdes full:** suíte completa rodada local (`make teste` / `artisan tests:run` / DUnit). Falha causada pelo diff = bloqueia.
7. **Segurança:** todo `id/slug` filtrado por dono + tenant/loja. Trocar ID na URL não pode vazar dado de outro.
8. **Migração declarada:** todo schema vem com procedimento: script, ordem deploy, compatibilidade com dado legado, rollback sem perda (`down()` defensivo).
9. **Pipeline só app:** banco é externo e já rodando. Sem amarrar container DB, sem env duplicada (`DB_PASSWORD` vs `DB_ROOT_PASSWORD`), runner/host corretos, flexível pra RDS.

## 1. Dor primeiro (lição do !124)

Descrição toda abre com:

```
Dor: quando dói, com que comando, qual erro
Repro: comando 1 + comando 2 + erro colado
Por que assim: por que flock+recreate e não X
Prova: rodei A+B em paralelo / vídeo / log
Fora de escopo: o que NÃO é e vai em MR própria
Refs: #XXXX + link TomTicket
```

Título = dor + ticket: `fix: serializa testes que disputam mesmo banco tt-4283`. Nunca `Estabiliza testes`, `Ajustes diversos`, `WIP`.
Solução sem dor reproduzível = bloqueia, mesmo com código certo.

## 2. Baby steps (lição do Ello master)

Padrão Clayton provado no GitLab (`b31ac9ea` +119/-13, `7a1e0785` +78/-0, `76a6d995` +24/-10, `643951e8` + teste junto):

- 1 ticket grande vira escadinha: 1. schema/patch.sql → 2. model/regra + teste → 3. tela/API → 4. nomenclatura/limpeza.
- Pode commitar tudo junto, revisar e quebrar depois. O que não pode é 1 commitzão de +3000 como `!104`.
- Mensagem explica porquê: SEFAZ, rejeição, `SetCaption` ignora em silêncio, etc.
- Estatística alvo: ~10 commits por MR (Clayton 307/24), não 1 commit por MR (atual 116/82).

## 3. Clareza e fonte única

- Intenção explícita: fixa `hoje/amanha` no `_before`, atribuição direta, sem helper esperto.
- Português total no nome se o módulo é PT (`repetirPedido`, não `getBairro`). Nunca mistura no mesmo identificador.
- Mesma info calculada 1x só: lista vs detalhe, API vs tela. Constante/query duplicada = reusar `TCliente`, `ExlStrUtils`, `OrderBySQL`, evento existente.
- Indireção só se agrega: wrapper de 1 linha que só repassa = usa direto.
- Comentário só se regra de negócio não óbvia, 1-2 linhas simples. Repete código = remove.
- Código morto = remove: função não chamada (`formatarTempo`), param ignorado, view órfã.

## 4. Performance e produção

- Nada que roda por request sem necessidade: `md5_file`/`lastModified` em todo request = gerar 1x ao salvar logo.
- `flock` em `/tmp` dentro do container só vale no mesmo container — declara ressalva.
- Mudança em `filesystems.php`, disco `public`, `TreatImageProduct`, SMTP, pagamento = análise de arquivos já em produção + migração.
- Quebra de contrato API (PDV/ERP) = seção própria: o que quebra, quem consome, mantém formato antigo por quanto tempo.

## 5. Template de abertura (mercado)

```
Projeto: [namespace/repo] | Origem → Destino | Assignee: eu | Reviewer: claytonaalves
Título: tipo/contexto-curto-tt-XXXX
Descrição:
  O que / Por que:
  Como testar (passo + massa):
  Impacto produção:
  Rollback:
  Refs: #XXXX + TomTicket
```

Rebaseado no dia, sem conflito, sem `merge main into...`, Draft só pra CI, pronto = marca reviewer. UI = antes/depois + URL homologação HTTPS.

## 6. Checklist 2 min (pré-MR)

[ ] <300 linhas, 1 ticket, 1 responsabilidade?
[ ] refactor/chore separado?
[ ] dor + repro + prova na descrição?
[ ] cada commit compila e testa sozinho?
[ ] testes full verdes?
[ ] ID trocado na URL não vaza?
[ ] sem duplicação, sem morto, sem wrapper inútil?
[ ] pipeline só app, env certo?
[ ] schema com migração + rollback?
[ ] sem dev-tool no prod?
[ ] UI com evidência?

1 não = não abre. Fatiar primeiro.

## 7. Tom do revisor

Educado e direto como ele: elogia `down()` defensivo e cobertura robusta, mas barra MR grande, overengineering e precedente de ferramenta (só Claude Code é homologado). Admite quando não testou local. Usa `suggestion` aplicável — se sugeriu código, aplica literal.
