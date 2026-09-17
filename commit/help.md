# /commit

```
/commit [instrução opcional sobre o commit]
```

Avalia os pendentes, separa em branches/MRs empilhadas (ideal 1 branch = 1 MR = 1 commit, com grafo do plano) e cria o commit seguindo o padrão [Conventional Commits](https://www.conventionalcommits.org/), analisando o diff real das mudanças (staged ou não) para montar tipo, escopo e descrição — nunca só pelo pedido do usuário ou nomes de arquivo.

**Exemplos:**
```
/commit
/commit separar em MRs empilhadas, uma por área alterada
/commit foca a mensagem na correção do bug de paginação
```

Se detectar múltiplas alterações lógicas distintas no diff, sugere dividir em commits menores ou em branches/MRs menores (empilhadas se dependentes) antes de montar a mensagem.
