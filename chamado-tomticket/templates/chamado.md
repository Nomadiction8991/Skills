Este template tem duas partes fixas e prontas — não é para reconstruir a formatação a cada chamado, só preencher os `[colchetes]`:

1. **Preview (Markdown)** — o que se exibe ao usuário no terminal para aprovação (Passo 5 de `referencias/abrir.md`).
2. **Payload (HTML)** — o que de fato vai no campo `mensagem` de `criar_chamado`/`editar_chamado`. Sempre usar exatamente esta estrutura, só trocando o conteúdo entre `[colchetes]`.

Metadados (Título/Cliente/Departamento/Categoria/Prioridade/Responsável/Ambiente) **não entram no HTML** — são campos separados da API, só aparecem no preview.

Se houver imagem(ns) a inserir, ver `../referencias/imagens.md`. Para **mais de uma** imagem (galeria), usar o template pronto em `galeria-imagens.md` (mesma pasta) em vez de empilhar `<img>` avulsos.

---

## 1. Preview (Markdown — exibir ao usuário)

```
Título       : Sistema (Módulo) - Descrição concisa do chamado
Cliente      : [Ello Tecnologia | Nome do cliente]
Departamento : [Departamento selecionado]
Categoria    : [Categoria | Não aplicável]
Prioridade   : [urgente | alta | normal | baixa]
Responsável  : [Nome do operador | Não atribuído]
Ambiente     : [Produção | Homologação | Desenvolvimento | Não informado]

**Descrição**
[Contexto do problema, módulo afetado, frequência e quando começou.]

**Imagens** (só quando houver galeria — ver templates/galeria-imagens.md; nunca colar HTML na prévia)
1. [Breve descrição do que a imagem mostra]
2. [Breve descrição do que a imagem mostra]
[seção omitida quando não há imagem/galeria]

**Passos para Reproduzir**
1. [Primeiro passo]
2. [Segundo passo]
3. [Terceiro passo]
[seção omitida quando não aplicável — ver referencias/abrir.md item f]

**Comportamento Esperado**
[O que deveria acontecer normalmente]

**Comportamento Observado**
[O que está acontecendo]

**Impacto**
- Usuários afetados: [número ou descrição]
- Ambiente: [Produção | Homologação | Desenvolvimento | Não informado]
- Processos críticos: [Sim | Não]
- Justificativa: [Por que esta prioridade foi escolhida]

**[Rótulo ad-hoc, ex: Solicitado por]** (opcional — só quando fizer sentido no contexto)
[Conteúdo na linha de baixo, nunca colado no rótulo]
```

---

## 2. Payload (HTML — vai literalmente para `mensagem`)

```html
<h4><span style="color: rgb(52, 152, 219); font-size: 14px;"><strong>Descrição</strong></span></h4>
<p>[Contexto do problema, módulo afetado, frequência e quando começou.]</p>

<h4><span style="color: rgb(52, 152, 219); font-size: 14px;"><strong>Passos para Reproduzir</strong></span></h4>
<ol>
  <li>[Primeiro passo]</li>
  <li>[Segundo passo]</li>
  <li>[Terceiro passo]</li>
</ol>
<!-- seção inteira (h4 + ol) omitida quando não aplicável — ver referencias/abrir.md item f -->

<h4><span style="color: rgb(52, 152, 219); font-size: 14px;"><strong>Comportamento Esperado</strong></span></h4>
<p>[O que deveria acontecer normalmente.]</p>

<h4><span style="color: rgb(52, 152, 219); font-size: 14px;"><strong>Comportamento Observado</strong></span></h4>
<p>[Descrição do que acontece de errado.]</p>

<h4><span style="color: rgb(52, 152, 219); font-size: 14px;"><strong>Impacto</strong></span></h4>
<ul>
  <li><strong>Usuários afetados:</strong> [número ou descrição]</li>
  <li><strong>Ambiente:</strong> [Produção | Homologação | Desenvolvimento | Não informado]</li>
  <li><strong>Processos críticos:</strong> [Sim | Não]</li>
  <li><strong>Justificativa:</strong> [Por que esta prioridade foi escolhida]</li>
</ul>

<h4><span style="color: rgb(52, 152, 219); font-size: 14px;"><strong>[Rótulo ad-hoc, ex: Solicitado por]</strong></span></h4>
<p>[Conteúdo]</p>
<!-- seção inteira omitida quando não houver rótulo ad-hoc aplicável -->
```

**Regras fixas deste HTML (não alterar a estrutura, só o conteúdo):**
- Todo cabeçalho é `<h4><span style="color: rgb(52, 152, 219); font-size: 14px;"><strong>...</strong></span></h4>` — nunca `style` direto no `<h4>` (é descartado silenciosamente pelo Froala, ver `referencias/ferramentas.md`).
- "Comportamento Observado" só usa `<blockquote>` quando houver uma mensagem de erro literal exibida ao usuário; caso contrário, só `<p>`.
- Listas são sempre `<ol>`/`<ul>` reais, nunca `1. texto`/`- texto` soltos.
- Se alguma seção não se aplicar (Passos para Reproduzir sem aplicação, sem rótulo ad-hoc), **omitir a seção inteira** (`<h4>` + conteúdo), não deixar `<h4>` vazio nem escrever "Não aplicável" dentro do HTML — no preview em Markdown, sim, pode aparecer "Não aplicável" como texto do item.
