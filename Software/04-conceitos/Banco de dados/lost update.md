# "Eu li, mas alguém mudou antes de eu alterar"
## Problema

Saldo = 1000

A lê 1000
B lê 1000

A soma 100 → 1100
B soma 200 → 1200

A salva 1100
B salva 1200

> O problema de Lost Update ocorre quando duas transações concorrentes lêem o mesmo dado e se sobrescrevem, fazendo com que a primeira transação se perca completamente
## Solução
### Lock pessimista:
A leitura do dado também trava ele para aquela transação, previnindo que alguém altere aquele valor enquanto a transação acontece

```sql
BEGIN;
SELECT saldo FROM conta WHERE id = 1 FOR UPDATE;  -- A lê 100 e trava a linha

-- ... A calcula 100 + 50 ...

UPDATE conta SET saldo = 150 WHERE id = 1;
COMMIT;  -- só aqui a trava é liberada
```

Uma vez que A e B tentarem atualizar o saldo usando version 5, vão se conflitar e capturamos o problema.

**⚠️ Risco de deadlock no caso de duas transações travarem linhas em ordens diferentes. Se a transação A trava a linha 1, depois a linha 2 e a transação B trava a linha 2 e depois a 1, o banco vai detectar e abortar uma delas.**

### sistema de versões:

| id  | balance | version |
| --- | ------- | ------- |
| 1   | 1000    | 5       |

Ao alterar o dado, teríamos algo como
```sql
UPDATE account
	SET
		balance = 1100,
		version = 6
	WHERE
		id = 1 AND
		version = 5
```

Uma vez que outros clients tentassem alterar essa informação da mesma forma, não encontrariam a versão 5 e retornaria rowcount 0. E aí a aplicação decide o que quer fazer com isso. Caso tente o retry com a versão mais atualizada do dado precisamos ter cuidado com o custo desse retry.
O sistema de versão funciona bem em casos com casos raros de conflitos. Em um caso em que a aplicação precise fazer uma chamada a um serviço externo no meio da transação, ela fica sujeita a essa latência no retry toda vez que uma referência a uma versão inexistente acontecer.
### Update atômico

```sql
UPDATE conta
	SET saldo = saldo + 50
	WHERE id = 1
```

O "saldo" do lado direito do operador de igualdade recupera seu valor durante a escrita, já segurando o lock daquela linha.
**Leitura, cálculo e gravação acontecem como uma unidade indivisível dentro do engine**. Não existe janela entre "ler" e "gravar" para outra transação se intrometer.