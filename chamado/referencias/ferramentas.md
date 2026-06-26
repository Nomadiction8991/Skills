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
<h4 style="font-size: 14px;">Título H4 (reduzido)</h4>
```

> **Recomendação:** usar `<h4 style="font-size: 14px;">` para seções dentro do corpo do chamado. H1–H3 ficam grandes demais e o `<h4>` padrão ainda é maior que o texto normal.

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

```html
<h4 style="font-size: 14px;">Descrição</h4>
<p>[Contexto do problema, módulo afetado, frequência e quando começou.]</p>

<h4 style="font-size: 14px;">Passos para Reproduzir</h4>
<ol>
  <li>Acesse <strong>[Módulo]</strong></li>
  <li>Clique em <strong>[Ação]</strong></li>
  <li>Preencha <strong>[Campo]</strong> com <code>[valor]</code></li>
  <li>Confirme — o problema ocorre aqui</li>
</ol>

<h4 style="font-size: 14px;">Comportamento Esperado</h4>
<p>[O que deveria acontecer normalmente.]</p>

<h4 style="font-size: 14px;">Comportamento Observado</h4>
<blockquote><p>"[Mensagem de erro exibida ao usuário, se houver]"</p></blockquote>
<p>[Descrição do que acontece de errado.]</p>

<h4 style="font-size: 14px;">Impacto</h4>
<ul>
  <li><strong>Usuários afetados:</strong> [número ou descrição]</li>
  <li><strong>Ambiente:</strong> [Produção / Homologação / Desenvolvimento]</li>
  <li><strong>Processos críticos:</strong> [Sim / Não]</li>
  <li><strong>Justificativa:</strong> [Por que esta prioridade foi escolhida]</li>
</ul>

<hr>

<p>⚠️ <strong>AVISO — CHAMADO GERADO POR INTELIGÊNCIA ARTIFICIAL</strong><br>
Este chamado foi aberto com auxílio de IA e pode conter erros ou inconsistências técnicas. Recomenda-se que um responsável técnico valide as informações antes do início do atendimento.</p>
```

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
