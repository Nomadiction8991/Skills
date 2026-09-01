# Referência — Imagens em Chamados

Como decidir quando inserir uma imagem enviada pelo usuário num chamado (abertura, edição ou comentário) e como executar o upload/embutimento tecnicamente.

---

## Quando isso se aplica

- Usuário anexa uma imagem na conversa **e** pede explicitamente para colocá-la no chamado → sempre seguir este fluxo (após confirmação padrão).
- Usuário anexa uma imagem sem pedir explicitamente, mas a imagem é pertinente ao chamado em questão (print do erro, evidência do defeito, comparação antes/depois, tela com o problema visível) → **sugerir** a inclusão, nunca inserir sem perguntar.
- Imagem irrelevante ao chamado (assunto não relacionado, imagem pessoal, print de outro contexto) → não sugerir, não inserir.

A avaliação de pertinência é sua (do assistente) — mas a decisão de inserir é sempre do usuário.

---

## Regra geral por tipo de arquivo

- **Imagem** (png, jpg, jpeg, gif, webp, etc.) → embutir no HTML via `<img>` (fluxo completo abaixo).
- **Qualquer outro tipo de arquivo** (pdf, log, doc, planilha, zip, etc.) → não embutir — ver `anexos.md` (anexo real via `arquivos`/`uploads`, sem tag no corpo).

---

## Regra de confirmação

Vale a regra geral #1 (`regras-gerais.md`) sem exceção: nunca subir/embutir imagem sem confirmação explícita, mesmo achando que é uma boa ideia.

> "Essa imagem parece relevante para o chamado — quer que eu inclua no [corpo do chamado / comentário]? `[S] Sim` `[N] Não`"

---

## Onde posicionar a imagem na estrutura

- **Abertura/edição** (corpo estruturado, ver `../templates/chamado.md`): logo após a seção **Comportamento Observado** quando a imagem evidencia o defeito, ou após **Descrição** quando ilustra o cenário geral. Nunca dentro de **Impacto** ou **Passos para Reproduzir**.
- **Múltiplas imagens (galeria, ver Passo 4b)**: usar uma seção própria `<h4>Imagens</h4>` (mesmo padrão de cabeçalho azul das demais seções, ver `templates/chamado.md`), logo após **Descrição** — em vez de tentar encaixar a galeria inline dentro de outra seção.
- **Comentário**: no ponto do texto em que faz sentido — normalmente ao final da explicação textual relacionada à imagem.

---

## Passo 1 — Localizar o arquivo da imagem

Igual ao Passo 1 de `anexos.md`: localizar o caminho local a partir do contexto da mensagem/anexo. Sem o caminho local não é possível fazer upload via curl.

> ⚠️ **TESTADO E CONFIRMADO (2026-07-28):** uma imagem colada direto no chat/IDE (ex.: print colado no editor) chega ao assistente só como conteúdo visual — não existe um arquivo correspondente acessível no disco via Bash (`find` em `/tmp`, `/home` etc. não encontra nada). É preciso puxar do clipboard (ver abaixo, por SO) ou pedir o caminho.

### Linux — KDE Plasma (Klipper), TESTADO E CONFIRMADO (2026-07-28)

O Plasma guarda histórico de clipboard (texto e imagem) em SQLite, mesmo sem o binário `klipper` no PATH:

```bash
DB=~/.local/share/klipper/history3.sqlite
sqlite3 -readonly "$DB" "SELECT uuid, mimetypes FROM main ORDER BY added_time DESC LIMIT 10;"
```

Cada linha é uma entrada do histórico, mais recente primeiro. **Ler a lista de cima para baixo e parar na primeira entrada cujo `mimetypes` não contenha `image/`** (regra do usuário: 1ª imagem → segue, 2ª imagem → segue, 3ª imagem → segue, 4ª não é imagem → **para aqui**, mesmo que a 5ª fosse imagem). Todas as imagens coletadas antes da parada são candidatas.

Para cada `uuid` de imagem coletado, o arquivo real está em:
```bash
data_uuid=$(sqlite3 -readonly "$DB" "SELECT data_uuid FROM aux WHERE uuid='<uuid>' AND mimetype LIKE 'image/%' LIMIT 1;")
# arquivo em: ~/.local/share/klipper/data/<uuid>/<data_uuid>
```
(sem extensão — usar `file` para confirmar o tipo antes de subir.)

Se houver mais de uma imagem candidata, ver "Como apresentar as candidatas ao usuário" abaixo antes de inserir qualquer uma no chamado.

Fallback (sem Klipper, ex. outro DE, ou GNOME): usar só o clipboard atual via `wl-clipboard`:
```bash
wl-paste --type image/png > <caminho-scratchpad>/clipboard.png
```
Verificar com `file <caminho>` que o PNG saiu válido. Sem histórico aqui — só a última imagem copiada.

### Windows — histórico de clipboard (Win+V) via PowerShell

Pré-requisito: histórico de área de transferência ligado (Configurações → Sistema → Área de Transferência → "Histórico do Clipboard"). Sem isso `GetHistoryItemsAsync` volta vazio.

```powershell
Add-Type -AssemblyName System.Runtime.WindowsRuntime
$asTaskGeneric = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where-Object {
    $_.Name -eq 'AsTask' -and $_.GetParameters().Count -eq 1 -and $_.GetParameters()[0].ParameterType.Name -eq 'IAsyncOperation`1'
})[0]
function Await($WinRtTask, $ResultType) {
    $t = $asTaskGeneric.MakeGenericMethod($ResultType).Invoke($null, @($WinRtTask))
    $t.Wait(-1) | Out-Null
    $t.Result
}

[Windows.ApplicationModel.DataTransfer.Clipboard,Windows.ApplicationModel.DataTransfer,ContentType=WindowsRuntime] | Out-Null
[Windows.Storage.Streams.RandomAccessStreamReference,Windows.Storage.Streams,ContentType=WindowsRuntime] | Out-Null

$history = Await ([Windows.ApplicationModel.DataTransfer.Clipboard]::GetHistoryItemsAsync()) ([Windows.ApplicationModel.DataTransfer.ClipboardHistoryItemsResult])

$i = 0
foreach ($item in $history.Items) {
    $content = $item.Content
    if ($content.Contains([Windows.ApplicationModel.DataTransfer.StandardDataFormats]::Bitmap)) {
        $streamRef = Await ($content.GetBitmapAsync()) ([Windows.Storage.Streams.RandomAccessStreamReference])
        $stream    = Await ($streamRef.OpenReadAsync()) ([Windows.Storage.Streams.IRandomAccessStreamWithContentType])
        $outPath   = "$env:TEMP\clip_$i.png"
        $netStream = [System.IO.WindowsRuntimeStreamExtensions]::AsStreamForRead($stream)
        $fileStream = [System.IO.File]::Create($outPath)
        $netStream.CopyTo($fileStream); $fileStream.Close()
        Write-Output "IMG $outPath"
        $i++
    } else {
        Write-Output "STOP (não é imagem)"
        break
    }
}
```

`$history.Items[0]` é a entrada mais recente do clipboard. O `foreach` segue a mesma regra do Klipper: enquanto o item for imagem (`Bitmap`), salva e continua; no primeiro que não for, para — mesmo que um item mais antigo depois volte a ser imagem. Rodar com `powershell.exe -sta` (a projeção WinRT exige apartment STA). Se `$history.Items` vier vazio, o histórico está desligado ou sem itens — cair no fallback de perguntar o caminho.

### Como apresentar as candidatas ao usuário

O usuário não vê as imagens do jeito que o assistente vê — não existe preview visual no terminal. Quando houver **mais de uma** imagem candidata (do histórico do clipboard, Klipper ou Windows), antes de inserir qualquer uma:

1. Abrir/ler cada imagem candidata (o assistente consegue ver o conteúdo da imagem) e escrever uma descrição breve de uma linha para cada uma (o que aparece: print de tela de X, foto de Y, gráfico de Z etc.).
2. Listar numerado e perguntar o que incluir:

> "Encontrei N imagens recentes no clipboard:
> `[1]` <descrição breve da imagem 1>
> `[2]` <descrição breve da imagem 2>
> `[3]` <descrição breve da imagem 3>
>
> Quais devo incluir no chamado? Responda com os números (ex.: `1,3`), `todas` ou `nenhuma`."

3. Incluir só as imagens cujos números o usuário informar (ou todas/nenhuma conforme resposta) — nunca assumir a lista inteira por padrão.

Se houver **só uma** candidata, ainda assim descrever brevemente antes de pedir a confirmação padrão da seção "Regra de confirmação" (não pular a descrição só porque é uma única imagem — o usuário continua sem enxergá-la).

### Se não houver imagem em nenhum clipboard/histórico

Não assumir que "o usuário anexou uma imagem" implica ter um caminho local disponível — **perguntar explicitamente o caminho do arquivo**. Sem caminho ou clipboard, o fluxo não pode continuar.

---

## Passo 2 — Escolher a forma de upload

| Situação | Forma |
|---|---|
| Só anexar em comentário, sem precisar embutir no texto, arquivo pequeno | `arquivos` do `comentar_chamado` (base64) — ver `anexos.md` |
| Embutir `<img>` no corpo (comentário, abertura ou edição), qualquer tamanho | `preparar_upload` + `curl` (Passo 3) — é a única forma que dá uma URL para colocar em `<img src="...">` |

---

## Passo 3 — Upload direto (`preparar_upload`)

Mecânica idêntica ao Passo 3 de `anexos.md` (exige `numero` já existente, mesmo `preparar_upload` + `curl`). Capturar o campo `file` do JSON retornado — aqui, além de servir para anexar (`uploads=[file, ...]`), também é usado para tentar extrair a URL pública a embutir no `<img>`.

> ✅ **TESTADO E CONFIRMADO (chamado #3927, 2026-07-28):** o campo `file.tmp_name` retornado pelo curl **já é** uma URL pública (`https://tomticket-anexos.s3.amazonaws.com/<hash>/<hash>/<hash>/<nome>`) diretamente utilizável em `<img src="...">` — não precisa montar nada a partir de outro campo. Confirmado com `curl -I` (200 OK) e com o `<img>` embutido de fato num chamado real via `editar_chamado`.
>
> ⚠️ **NOVO RISCO NÃO CONFIRMADO — validar em produção:** essa URL do S3 volta com `x-amz-expiration` e `rule-id="DeleteTemporary"` (expira em ~2 dias). O upload só é "finalizado"/tornado permanente quando passa por `comentar_chamado(upload_id=..., uploads=[file])` — e o fluxo de abertura/edição abaixo **nunca chama `comentar_chamado`**, só embute a URL crua no `<img>` via `editar_chamado`. Não está confirmado se a imagem embutida por essa via continua acessível depois do prazo de expiração, ou se vira link quebrado no chamado. **Ação pendente:** conferir o chamado #3927 depois de 2026-07-30 e ver se a imagem ainda carrega; se quebrar, o fluxo de abertura/edição precisa ser ajustado para também chamar `comentar_chamado` (só para finalizar o upload, não necessariamente como comentário visível) antes de embutir a URL.

---

## Passo 4 — Montar o HTML com a imagem embutida

```html
<img src="<URL retornada/confirmada>" style="width: 300px; display: block; margin: 5px auto; text-align: center;">
```

Ajustar `width` conforme o conteúdo (ex.: 300px para print de tela comum; maior se precisar de detalhe fino).

---

## Passo 4b — Galeria de múltiplas imagens (lado a lado)

Quando houver **mais de uma** imagem a inserir (ex.: várias candidatas aprovadas pelo usuário no Passo 1), usar uma galeria em vez de empilhar `<img>` um embaixo do outro. **Template pronto para copiar/colar:** `../templates/galeria-imagens.md` — só duplicar o bloco de miniatura por imagem e trocar as URLs.

> **Padrão:** tabela de largura fixa em pixel (`table-layout: fixed`, ex. `660px` = 3 colunas × `220px`), uma imagem por célula, `<img>` **direto** (sem `<div>`/`background-image`) com `style="max-width: 220px; max-height: 220px;"` — encolhe proporcionalmente pra caber no quadrado, sem distorcer e sem estourar em nenhuma dimensão. **Sem `<a>`/link nenhum** — só visualização estática, sem clique. Ver o HTML completo (copiar/colar) em `../templates/galeria-imagens.md`.
>
> Não usar `<div>`/`background-image`, `<a href>`, `<style>` nem `<details>/<summary>` nesse contexto — ver "O que NÃO fazer" em `../templates/galeria-imagens.md`.

---

## Passo 5 — Fluxo por operação

### Comentário (mais simples — chamado já existe)

1. `preparar_upload(numero)` → curl → capturar `file`
2. Montar a mensagem em HTML com o `<img>` embutido no ponto certo do texto
3. `comentar_chamado(numero, mensagem, upload_id=..., uploads=[file])` — enviar também `upload_id`/`uploads` garante que a imagem fique como anexo real, além de embutida

### Abertura (`criar_chamado`)

`criar_chamado` não tem parâmetro de arquivo, e `preparar_upload` exige `numero` — logo é impossível subir a imagem antes do chamado existir. Fluxo:

1. `criar_chamado(...)` sem imagem, seguindo `abrir.md` normalmente
2. Anotar o `numero` retornado
3. `preparar_upload(numero)` → curl → capturar `file`/URL
4. `editar_chamado(numero, mensagem="<corpo completo já com <img> no lugar certo>")` — corpo completo, reescrito do zero, seguindo `editar.md` (nunca só o trecho novo)

> ⚠️ **`editar_chamado` não tem parâmetro de upload/anexo (só `numero`/`titulo`/`mensagem`, ver schema real da tool)** — a imagem fica embutida no HTML via `<img>`, mas **não** vira anexo real do chamado por essa via. Se também precisar do anexo real (arquivo baixável, não só a tag `<img>`), é preciso um comentário adicional (`comentar_chamado(numero, mensagem, upload_id=..., uploads=[file])`) — só `comentar_chamado` aceita `arquivos`/`upload_id`/`uploads`.

### Edição (`editar_chamado`)

Igual aos passos 3-4 da abertura, mas o chamado já existe: `preparar_upload(numero)` → curl → `editar_chamado` com o corpo completo incluindo o `<img>`. Vale a mesma ressalva acima — sem anexo real via `editar_chamado`.

---

## Passo 6 — Validar antes de enviar

Aplicar `checklist-html.md` normalmente sobre o HTML final, agora incluindo a tag `<img>`.

---

## Alternativa — base64 inline (`data:image/...`) — evitar

HTML permite `<img src="data:image/png;base64,...">`, mas evitar essa forma para embutir no corpo/comentário:

- Aumenta muito o tamanho do payload da mensagem
- Os exemplos reais observados no TomTicket usam URL S3 real, não base64, no `<img>` embutido pelo Froala
- Não há confirmação de que o editor renderize data URIs da mesma forma que uma URL normal

`base64` continua sendo o formato correto para o parâmetro `arquivos` do `comentar_chamado` (conteúdo de arquivo pequeno) — isso não muda; a ressalva acima é só sobre usar base64 dentro do `<img src="...">`.

---

## Notas

- Nunca inserir imagem sem confirmação explícita do usuário (regra geral #1).
- URL do Passo 3 confirmada (chamado #3927), mas com risco de expiração em ~2 dias ainda não validado — ver aviso no Passo 3 antes de repetir o fluxo de abertura/edição em chamado importante.
