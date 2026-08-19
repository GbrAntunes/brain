# "Eu li, mas alguém mudou antes de eu alterar"
## Problema

Saldo = 1000

A lê 1000
B lê 1000

A soma 100 → 1100
B soma 200 → 1200

A salva 1100
B salva 1200

## Solução
Lock pessimista usando `SELECT ... FOR UPDATE` ou controle otimista, usando uma versão:

| id  | balance | version |
| --- | ------- | ------- |
| 1   | 1000    | 5       |

Uma vez que A e B tentarem atualizar o saldo usando version 5, vão se conflitar e capturamos o problema.