# Referência — Ferramentas de Formatação

Guia de formatação HTML para o corpo dos chamados no TomTicket.

> **Editor:** TomTicket usa o **Froala Editor** (WYSIWYG HTML). O conteúdo é armazenado e enviado como **HTML**, não Markdown.

---

## Comportamento da API MCP (leitura vs. escrita)

| Operação | Comportamento |
|----------|---------------|
| **Escrita** (`criar_chamado`, `editar_chamado`, `comentar_chamado`) | Enviar HTML. O Froala armazena e renderiza HTML nativamente. |
| **Leitura** (`ver_chamado`, `listar_chamados`) | A API **descarta toda a formatação** e retorna apenas texto plano. Só texto e quebras de parágrafo são preservados. |
| **Emojis** | Preservados na leitura (retornam como caracteres Unicode). ✅ |
| **Quebras de parágrafo** | Preservadas na leitura (linhas em branco). ✅ |

**Consequência prática:** ao ler um chamado via MCP, não é possível ver a formatação original. A análise de chamados via API deve considerar apenas o conteúdo textual.

---

## Elementos HTML suportados pelo Froala

### Formatação inline de texto

```html
<strong>negrito</strong>
<em>itálico</em>
<u>sublinhado</u>
<s>tachado</s>
<sub>subscrito</sub>
<sup>sobrescrito</sup>
```

### Cor e destaque

```html
<span style="color: rgb(251, 160, 38);">texto colorido</span>
<span style="background-color: rgb(251, 160, 38);">texto destacado</span>
```

### Famílias de fonte (via inline style)

```html
<span style="font-family: Georgia, serif;">Georgia</span>
<span style="font-family: Impact, Charcoal, sans-serif;">Impact</span>
<span style="font-family: Tahoma, Geneva, sans-serif;">Tahoma</span>
<span style="font-family: Verdana, Geneva, sans-serif;">Verdana</span>
<span style="font-family: Courier, Courier New, Lucida Console, Monaco, Consolas, Inconsolata;">Courier (monospace)</span>
```

### Tamanho de fonte

```html
<span style="font-size: 14px;">texto menor</span>
```

---

### Parágrafos e alinhamento

```html
<p>padrão (esquerda)</p>
<p style="text-align: center;">centralizado</p>
<p style="text-align: right;">direita</p>
<p style="text-align: justify;">justificado</p>
```

### Espaçamento entre linhas

```html
<p style="line-height: 1;">compacto</p>
<p style="line-height: 1.15;">normal</p>
<p style="line-height: 1.5;">confortável</p>
<p style="line-height: 2;">espaçado</p>
```

### Indentação

```html
<p style="margin-left: 20px;">um nível de indentação</p>
<p style="margin-left: 40px;">dois níveis de indentação</p>
```

---

### Títulos

```html
<h1>Título H1</h1>
<h2>Título H2</h2>
<h3>Título H3</h3>
<h4><span style="font-size: 14px; color: rgb(52, 152, 219);"><strong>Título H4 (reduzido, azul)</strong></span></h4>
```

> ⚠️ **REGRA CRÍTICA — TESTADA E CONFIRMADA EM PRODUÇÃO (chamado #3882, 2026-07-14):** o Froala/TomTicket **descarta silenciosamente** qualquer `style` colocado direto na tag de heading (`<h1>`–`<h4>`). `<h4 style="font-size: 14px; color: ...">` é renderizado **sem cor e no tamanho padrão** — o atributo é aceito na escrita, mas desaparece na renderização. Não há erro, não há aviso: parece certo até alguém abrir o chamado no navegador.
>
> **Isso já causou um chamado criado com formatação errada** (título sem cor/tamanho reduzido) porque a orientação antiga desta seção mandava estilizar o `<h4>` diretamente.
>
> **Regra a seguir sempre, sem exceção:** todo `color`/`font-size`/`font-family` em heading (`<h1>`–`<h4>`) vai num `<span>` **dentro** do heading, nunca no `style` do próprio heading:
> ```html
> <!-- ❌ ERRADO — estilo descartado silenciosamente -->
> <h4 style="font-size: 14px; color: rgb(52, 152, 219);">Descrição</h4>
>
> <!-- ✅ CORRETO — estilo preservado -->
> <h4><span style="font-size: 14px; color: rgb(52, 152, 219);"><strong>Descrição</strong></span></h4>
> ```
> Isso vale para **qualquer** heading em qualquer chamado, não só para o template de seções abaixo. Se em algum momento futuro parecer necessário estilizar um heading diretamente, essa regra prevalece — não repetir o erro.

---

### Listas ordenadas

```html
<!-- Numérica (padrão) -->
<ol>
  <li>Item 1</li>
  <li>Item 2</li>
</ol>

<!-- Letras minúsculas: a, b, c... -->
<ol style="list-style-type: lower-alpha;">
  <li>Item</li>
</ol>

<!-- Letras gregas: α, β, γ... -->
<ol style="list-style-type: lower-greek;">
  <li>Item</li>
</ol>

<!-- Romanos minúsculos: i, ii, iii... -->
<ol style="list-style-type: lower-roman;">
  <li>Item</li>
</ol>

<!-- Letras maiúsculas: A, B, C... -->
<ol style="list-style-type: upper-alpha;">
  <li>Item</li>
</ol>

<!-- Romanos maiúsculos: I, II, III... -->
<ol style="list-style-type: upper-roman;">
  <li>Item</li>
</ol>
```

### Listas não ordenadas

```html
<!-- Disco (padrão) -->
<ul>
  <li>Item</li>
</ul>

<!-- Círculo vazio -->
<ul style="list-style-type: circle;">
  <li>Item</li>
</ul>

<!-- Disco sólido -->
<ul style="list-style-type: disc;">
  <li>Item</li>
</ul>

<!-- Quadrado -->
<ul style="list-style-type: square;">
  <li>Item</li>
</ul>
```

---

### Bloco de código / pré-formatado

```html
<pre>código ou stack trace aqui</pre>
```

### Citação

```html
<blockquote><p>Mensagem de erro exibida ao usuário.</p></blockquote>
```

### Link

```html
<a target="_blank" href="https://exemplo.com">texto do link</a>
```

### Linha divisória

```html
<hr>
```

---

### Tabelas

```html
<table style="width: 100%;">
  <tbody>
    <tr>
      <td style="width: 25%;">Campo</td>
      <td style="width: 25%;">Esperado</td>
      <td style="width: 25%;">Observado</td>
      <td style="width: 25%;">Impacto</td>
    </tr>
    <tr>
      <td>Total</td>
      <td>R$ 150,00</td>
      <td style="background-color: rgb(26, 188, 156);">R$ 0,00</td>
      <td>Alto</td>
    </tr>
  </tbody>
</table>
```

> Células suportam `background-color` para destacar valores incorretos.

### Emoji

Inserir diretamente como caractere Unicode — o Froala os trata como texto:

```html
<p>Erro crítico 🔴 encontrado em produção 😯</p>
```

---

## Estrutura recomendada do corpo (HTML completo)

> **Cabeçalhos de seção:** sempre em azul negrito e menores que o corpo do texto (`color: rgb(52, 152, 219); font-size: 14px;` + `<strong>`), para reproduzir visualmente o mesmo destaque do preview exibido no terminal antes da criação do chamado.
>
> **Importante:** o Froala/TomTicket descarta o atributo `style` colocado diretamente na tag `<h4>` (cor e tamanho não são aplicados ao renderizar). Colocar `color` e `font-size` num `<span>` interno, dentro do `<h4>` — só assim o estilo é preservado.

Ver `../templates/chamado.md` (Seção 2 — Payload HTML) para a estrutura fixa completa, pronta para só preencher os `[colchetes]`.

> Rótulo ad-hoc (ex.: "Solicitado por") segue o mesmo padrão de cabeçalho azul `<h4>` das demais seções — não é `<strong>Rótulo:</strong> conteúdo` inline, é cabeçalho + parágrafo separado, igual ao resto do corpo.

Título/Cliente/Departamento/Categoria/Prioridade/Responsável/Ambiente **não** entram no corpo — são campos reais da API (`criar_chamado`), preenchidos separadamente. O corpo começa em "Descrição".

---

### Rótulo solto dentro do corpo/mensagem (`Campo:`)

Isso vale só para rótulos **dentro do corpo/mensagem do chamado** (o texto que vai para `mensagem` na API) — não para o bloco de metadados do topo (Cliente/Departamento/Categoria/Prioridade/Responsável/Ambiente), que são campos separados da API e continuam no formato `Label : valor` do `../templates/chamado.md`, mesma linha.

Um rótulo ad-hoc dentro da mensagem (ex.: `Solicitado por:`, quando não é uma das seções padrão) tem o conteúdo na **linha de baixo**:
```
Solicitado por:
André (equipe de desenvolvimento)
```
```html
<p><strong>Solicitado por:</strong></p>
<p>André (equipe de desenvolvimento)</p>
```

As seções padrão do corpo (Descrição, Passos para Reproduzir, Comportamento Esperado, Comportamento Observado, Impacto) **não levam `:`** no cabeçalho — ver `../templates/chamado.md`: é só o nome da seção, com o conteúdo já na linha/parágrafo seguinte por natureza do template. Dentro de Impacto, os itens continuam `- Campo: valor` na mesma linha (lista curta).

---

## Compatibilidade com tema escuro e claro

> **Regra crítica:** O TomTicket pode estar em tema escuro ou claro. **Nunca use cores de fundo claras (pastel)** em tabelas ou textos — elas ficam invisíveis no tema escuro. Prefira sempre **cor no texto** em vez de cor no fundo.

### Paleta de cores segura para ambos os temas

Use as cores abaixo em `color` (texto). Elas têm contraste suficiente tanto em fundo escuro quanto em fundo claro:

| Semântica | Cor | RGB | Uso típico |
|-----------|-----|-----|------------|
| Alerta / erro | 🔴 Vermelho | `rgb(220, 53, 69)` | Erros, problemas, campos duplicados |
| Atenção / destaque | 🟠 Laranja | `rgb(251, 160, 38)` | Módulos, nomes de telas importantes |
| Sucesso / ok | 🟢 Verde | `rgb(40, 167, 69)` | Resolvido, funcional, positivo |
| Informação | 🔵 Azul | `rgb(52, 152, 219)` | Parâmetros, links, contexto neutro |
| Advertência | 🟡 Amarelo | `rgb(241, 196, 15)` | Atenção moderada, pendências |

### Em tabelas: usar cor no texto, não no fundo

❌ **Evitar** — fundo claro, invisível no tema escuro:
```html
<td style="background-color: rgb(212, 237, 218);">Resolvido</td>
<td style="background-color: rgb(255, 235, 205);">Melhoria de UX</td>
```

✅ **Correto** — cor no texto, visível em ambos os temas:
```html
<td><span style="color: rgb(40, 167, 69);"><strong>✓ Resolvido</strong></span></td>
<td><span style="color: rgb(251, 160, 38);"><strong>Melhoria de UX</strong></span></td>
<td><span style="color: rgb(220, 53, 69);"><strong>⚠ Crítico</strong></span></td>
```

### Fundo de cabeçalho de tabela

Para o `<tr>` de cabeçalho, use fundo cinza médio (visível em ambos os temas):

```html
<tr style="background-color: rgb(80, 80, 80);">
  <td><strong style="color: #fff;">Aspecto</strong></td>
  <td><strong style="color: #fff;">Descrição</strong></td>
  <td><strong style="color: #fff;">Status</strong></td>
</tr>
```

---

## Resumo de boas práticas

| Elemento | Recomendado | Evitar |
|----------|-------------|--------|
| Seções do chamado | `<h4 style="font-size: 14px;">` | `<h1>`, `<h2>`, `<h3>`, `<h4>` padrão (todos grandes demais) |
| Listagem de impacto | `<ul>` com `<strong>` | `<table>` (desnecessário) |
| Comparação de valores | `<table>` | texto corrido |
| Mensagens de erro | `<blockquote>` + `<pre>` | texto simples |
| Nomes de campos/módulos | `<strong>` | sem destaque |
| Stack traces / JSON | `<pre>` | parágrafo normal |
| Passos sequenciais | `<ol>` | lista não ordenada |
| Cor em tabelas | `color` no texto | `background-color` claro (invisível no tema escuro) |
| Cabeçalho de tabela | fundo cinza médio `rgb(80,80,80)` + texto branco | fundo claro `rgb(248,249,250)` |
