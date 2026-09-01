# Baseline de Code Smells

O repositório tem prioridade. Um padrão documentado do repositório sempre vence; onde ele endossa algo que a baseline sinalizaria, suprima o smell.

Sempre uma questão de julgamento. Cada smell é uma heurística rotulada, nunca uma violação definitiva — e, como qualquer padrão aqui, pule o que o ferramental já fiscaliza.

Cada smell indica o que é → como corrigir; compare com o diff:

- **Nome Misterioso** — uma função, variável ou tipo cujo nome não revela o que faz ou contém. → renomeie; se nenhum nome honesto surgir, o design está nebuloso.
- **Código Duplicado** — a mesma forma lógica aparece em mais de um trecho ou arquivo na alteração. → extraia a forma compartilhada, chame-a de ambos os lugares.
- **Feature Envy** — um método que acessa mais dados de outro objeto do que os seus próprios. → mova o método para o objeto cujos dados ele inveja.
- **Aglomerados de Dados** — os mesmos campos ou parâmetros viajam sempre juntos (um tipo querendo nascer). → agrupe-os em um tipo, passe esse tipo.
- **Obsessão por Primitivos** — um primitivo ou string substituindo um conceito de domínio que merece seu próprio tipo. → dê ao conceito seu próprio tipo pequeno.
- **Switches Repetidos** — a mesma cascata `switch`/`if` sobre o mesmo tipo recorre na alteração. → substitua por polimorfismo, ou um mapa que ambos os pontos compartilhem.
- **Cirurgia com Espingarda** — uma mudança lógica força edições espalhadas por muitos arquivos no diff. → reúna o que muda junto em um módulo.
- **Mudança Divergente** — um arquivo ou módulo é editado por várias razões não relacionadas. → separe para que cada módulo mude por uma razão.
- **Generalidade Especulativa** — abstração, parâmetros ou hooks adicionados para necessidades que a especificação não tem. → delete; volte ao inline até surgir uma necessidade real.
- **Cadeias de Mensagens** — navegação longa `a.b().c().d()` da qual o chamador não deveria depender. → esconda a navegação atrás de um método no primeiro objeto.
- **Intermediário** — uma classe ou função que apenas delega adiante. → elimine, chame o alvo real diretamente.
- **Herança Recusada** — uma subclasse ou implementador que ignora ou sobrescreve a maior parte do que herda. → abandone a herança, use composição.
