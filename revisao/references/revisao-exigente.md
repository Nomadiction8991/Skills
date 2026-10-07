# Revisão Exigente — padrão para tudo

Vale para qualquer projeto/stack. Se passar aqui, passa em review sênior sem retrabalho.

## 0. Gates (falhou 1 = não abre MR)

1. **Tamanho:** MR >300 linhas ou >10 arquivos ou >1 responsabilidade = fatiar. Nenhum commit >200 linhas sem justificativa. Nenhum MR >1000 linhas nunca.
2. **1 commit = 1 conquista completa:** compila + teste verde sozinho, reversível sozinho. Ordem cronológica não importa.
3. **Refactor separado de feature:** `chore:`/`refactor:` em MR própria. `feat:`/`fix:` só com mínimo do ticket.
4. **Sem dev-tool no código prod:** sem rota extra pra túnel, sem reescrita global de URL, sem middleware só pra dev, sem plugin de hot-reload, sem script de boot multi-processo, sem imagem prod com pacote de teste, sem comando que quebra em worktree.
5. **Sem arquivo local/gerado:** sem ignore de agente, sem ajuste em `.gitignore` pra ferramenta, sem doc gerada, sem `console.log`/`TODO`/`credencial`.
6. **Testes verdes full:** suíte completa rodada local (ex.: `make teste`). Falha causada pelo diff = bloqueia.
7. **Segurança:** todo `id/slug` filtrado por dono + escopo (tenant/loja). Trocar ID na URL não pode vazar dado de outro.
8. **Migração declarada:** todo schema vem com procedimento: script, ordem deploy, compatibilidade com dado legado, rollback sem perda.
9. **Pipeline só app:** banco/serviço externo é considerado já rodando. Sem amarrar container de banco, sem env duplicada, sem host fixo, flexível pra trocar provedor.

## 1. Dor primeiro

Toda descrição abre com:

```
Dor: quando dói, com que comando, qual erro
Repro: comando 1 + comando 2 + erro colado
Por que assim: por que essa solução e não outra
Prova: rodei A+B, log/vídeo anexo
Fora de escopo: o que NÃO é e vai em MR própria
Refs: #XXXX + link do chamado
```

Título = dor + ticket (ex.: `fix: serializa testes que disputam mesmo banco tt-4283`). Nunca `Estabiliza`, `Ajustes diversos`, `WIP`.
Solução sem dor reproduzível = bloqueia, mesmo com código certo.

## 2. Baby steps

- 1 ticket grande vira escadinha: 1. schema/migration → 2. model/regra + teste → 3. tela/API → 4. nomenclatura/limpeza.
- Pode commitar tudo junto, revisar e quebrar depois. O que não pode é 1 commitzão gigante.
- Mensagem explica porquê, não só o quê.
- Alvo: vários commits pequenos por MR, não 1 commit por MR.

## 3. Clareza e fonte única

- Intenção explícita: fixa dado no setup, atribuição direta, sem helper esperto.
- Idioma consistente no nome (ver `validacoes.md#13`): nunca mistura dois idiomas no mesmo identificador.
- Mesma info calculada 1x só: lista vs detalhe, API vs tela. Duplicação hoje vira divergência amanhã.
- Indireção só se agrega: wrapper de 1 linha que só repassa = usa direto.
- Comentário só se regra de negócio não óbvia, 1-2 linhas simples. Repete código = remove.
- Código morto = remove: função não chamada, param ignorado, view órfã.

## 4. Performance e produção

- Nada pesado por request: calcula 1x ao salvar, não em toda página.
- Lock/arquivo temporário: declara onde vale (ex.: só no mesmo container).
- Mudança em storage, upload, e-mail, pagamento = análise de arquivos já em produção + migração.
- Quebra de contrato API = seção própria: o que quebra, quem consome, mantém formato antigo por quanto tempo.

## 5. Abertura (mercado)

```
Projeto | Origem → Destino | Assignee | Reviewer
Título: tipo/contexto-curto-tt-XXXX
Descrição: O que / Por que / Como testar / Impacto / Rollback / Refs
```

Rebaseado no dia, sem conflito, sem `merge base into...`, Draft só pra CI, pronto = marca reviewer. UI = antes/depois.

## 6. Checklist 2 min

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
