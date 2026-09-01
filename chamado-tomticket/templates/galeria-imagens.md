Template para inserir uma **galeria de múltiplas imagens** no corpo de um chamado (abertura/edição) ou comentário. Ver `../referencias/imagens.md` (Passo 4b) para quando usar e como conseguir as URLs (upload via `preparar_upload`+curl).

Não reconstruir a formatação do zero — só duplicar o bloco de uma célula por imagem e trocar o `src`.

**Vale igual para `criar_chamado`/`editar_chamado`/`comentar_chamado`** — o HTML da tabela é sempre o mesmo, só muda onde encaixar:
- **Abertura/edição**: dentro do corpo estruturado, com o `<h4>Imagens</h4>` (ver bloco abaixo) como seção própria logo após **Descrição** — ver `imagens.md`, "Onde posicionar".
- **Comentário**: colar só a `<table>` (sem o `<h4>Imagens</h4>`) direto no ponto do texto que fizer sentido — comentário não é seccionado como abertura/edição.

---

## Prévia em Markdown (exibir ao usuário antes de enviar)

A tabela HTML abaixo é só para o payload — nunca colar HTML cru na prévia mostrada ao usuário no terminal. Na prévia, representar a galeria como uma **lista numerada**, uma linha por imagem, com uma descrição breve do que ela mostra:

```
**Imagens**
1. [Breve descrição do que a imagem mostra]
2. [Breve descrição do que a imagem mostra]
3. [Breve descrição do que a imagem mostra]
```

---

## Padrão: tabela fixa + `<img>` com `max-width`/`max-height`

```html
<h4><span style="color: rgb(52, 152, 219); font-size: 14px;"><strong>Imagens</strong></span></h4>
<table style="width: 660px; table-layout: fixed; border-collapse: collapse; border: none;">
  <tbody>
    <tr>
      <td style="width: 220px; padding: 2px; text-align: center; border: none;"><img src="<URL 1>" style="max-width: 220px; max-height: 220px;"></td>
      <td style="width: 220px; padding: 2px; text-align: center; border: none;"><img src="<URL 2>" style="max-width: 220px; max-height: 220px;"></td>
      <td style="width: 220px; padding: 2px; text-align: center; border: none;"><img src="<URL 3>" style="max-width: 220px; max-height: 220px;"></td>
    </tr>
    <tr>
      <td style="width: 220px; padding: 2px; text-align: center; border: none;"><img src="<URL 4>" style="max-width: 220px; max-height: 220px;"></td>
      <td style="width: 220px; padding: 2px; border: none;"></td>
      <td style="width: 220px; padding: 2px; border: none;"></td>
    </tr>
  </tbody>
</table>
```

- **3 colunas fixas de 220px** (`table-layout: fixed` + `width: 660px` no `<table>`, `width: 220px` em cada `<td>`) — ajustar os dois números juntos se precisar de outro tamanho (sempre `largura_total = colunas × largura_célula`).
- A tabela do exemplo tem 2 linhas (4 imagens), mas **o número de linhas é livre** — adicionar quantas `<tr>` de 3 colunas forem necessárias pra caber todas as imagens (ex.: 7 imagens → 3 linhas, a última com 1 célula preenchida e 2 vazias).
- Cada imagem é um `<img>` **direto**, sem `<div>`/`background-image` — só `style="max-width: 220px; max-height: 220px;"`. Isso encolhe a imagem proporcionalmente pra caber dentro do quadrado sem distorcer e sem estourar em nenhuma das duas dimensões. `text-align: center` na `<td>` centraliza a imagem quando ela fica menor que a célula.
- Preencher célula por célula, esquerda pra direita, de cima pra baixo. Sobrando célula(s) na última linha, deixar `<td>` vazio — não quebra o layout.
- **NUNCA envolver a imagem em `<a href="...">`** — ver "O que NÃO fazer" abaixo.

---

## O que NÃO fazer

- **`<div>` com `background-image`** para simular a imagem (mesmo para crop/cover) — `<img>` direto com `max-width`/`max-height` é a única forma aprovada.
- **`<a href="<URL do S3>">`** (com ou sem `target="_blank"`) — a URL do upload tem `Content-Type: application/octet-stream`, então clicar sempre baixa o arquivo em vez de abrir/visualizar. Não adicionar link nenhum a este template.
- **`<style>`** em qualquer lugar da `mensagem` — quebra a renderização inteira do chamado.
- **`<details>/<summary>`** — não é renderizado/permitido pelo Froala.
- **`<img>` com atributos HTML `width`/`height`** (ex. `width="220" height="220"`, sem `style`) — só a largura é respeitada de fato; a altura fica livre e estoura a célula em imagens retrato. Usar `style="max-width/max-height"`.
- **`<img>` com `object-fit`/`height` fixo** dentro de `<td>` ou `<div>` para tentar crop.
- **Larguras percentuais na tabela** (`width: 33%`) — preferir pixel fixo (`table-layout: fixed` + `width` em px).

**Conclusão:** a galeria é sempre estática, sem interação — não existe forma de "clicar pra ver maior" sem cair em download ou quebrar o layout.
