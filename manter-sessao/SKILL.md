---
name: manter-sessao
description: "Instala no crontab do usuário um agendamento que envia uma mensagem mínima (\"oi\") ao Claude Code em horários fixos, para ancorar o reset da janela de uso de 5h perto do meio dos turnos de trabalho do usuário. Use quando o usuário pedir para manter/alinhar a sessão do Claude, resetar o limite de uso num horário específico, ou mencionar 'janela de 5h' / 'sessão de 5h' junto com turnos de trabalho."
---

# Manter Sessão (alinhar reset da janela de uso de 5h)

A conta Claude (Pro/Max) tem uma janela de uso de 5h que começa na primeira mensagem enviada e reseta 5h depois. Esta skill agenda pings automáticos (`claude -p "oi"`) via crontab do sistema para que esse reset caia perto do meio dos turnos de trabalho do usuário, em vez de num horário aleatório.

## Passo 1 — descobrir os turnos

Pergunte os turnos de trabalho do usuário, se ainda não souber (horário de início e fim de cada um).

## Passo 2 — calcular os horários de ping

1. Calcule o meio de cada turno.
2. A janela é fixa em 5h: uma vez ancorada por um ping, os resets seguintes ocorrem automaticamente a cada 5h — não é preciso um ping por turno se a diferença entre os meios dos turnos for múltiplo de 5h.
3. Escolha o horário do(s) ping(s) inicial(is) minimizando o desvio total entre os resets resultantes e os meios dos turnos.
4. Exemplo já validado com o usuário: turnos 7:30–11:30 e 13:30–18:00 → pings às **05:05** e **10:07** (o de 10:07 já é o próprio reset natural do ping das 05:05, servindo de âncora para o próximo reset em ~15:07).
5. Sempre confirme os horários calculados com o usuário antes de instalar — não instale silenciosamente um cálculo novo sem validação.

## Passo 3 — instalar no crontab

1. Verifique se já existem entradas desta skill no crontab (`crontab -l | grep manter-sessao-claude`) para não duplicar.
2. Adicione uma linha por horário, sempre com o comentário `# manter-sessao-claude` no final para permitir remoção seletiva depois:
   ```
   5 5 * * * claude -p "oi" > /dev/null 2>&1 # manter-sessao-claude
   7 10 * * * claude -p "oi" > /dev/null 2>&1 # manter-sessao-claude
   ```
3. Use `(crontab -l 2>/dev/null; echo "<linha>") | crontab -` para cada linha nova.
4. Depois de instalar, rode `crontab -l` e mostre ao usuário as linhas ativas.

## Importante

- Cada ping consome uma mensagem real da conta Claude do usuário — só instale com confirmação explícita do horário calculado.
- Nunca sobrescreva o crontab inteiro; sempre acrescente preservando entradas existentes de outros programas.
- Para desinstalar, remova só as linhas marcadas com `# manter-sessao-claude`: `crontab -l | grep -v manter-sessao-claude | crontab -`.
- Isso é independente do cron interno do Claude Code (`CronCreate`), que expira em 7 dias e só dispara com o REPL ocioso — o crontab do sistema roda sempre, sem expiração.
