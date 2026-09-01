# /commit

```
/commit [instrução opcional sobre o commit]
```

Cria um commit seguindo o padrão [Conventional Commits](https://www.conventionalcommits.org/), analisando o diff real das mudanças (staged ou não) para montar tipo, escopo e descrição — nunca só pelo pedido do usuário ou nomes de arquivo.

**Exemplos:**
```
/commit
/commit separar em dois commits, um por área alterada
/commit foca a mensagem na correção do bug de paginação
```

Se detectar múltiplas alterações lógicas distintas no diff, sugere dividir em commits menores antes de commitar.
