O "I" do ACID em [[Transaction]] não é ligado/desligado: é um botão com níveis, e o default do Postgres (`READ COMMITTED`) é o mais fraco deles.

### O que o READ COMMITTED garante (e o que não garante)
Ele garante apenas que você não lê dado não commitado por outra transação. **Não garante** que o dado que você leu continue válido até o `COMMIT`.

O caso clássico:

```SQL
BEGIN;
	SELECT balance FROM accounts WHERE id = 1;   -- lê 100
	-- outra transação debita 80 e commita aqui
	UPDATE accounts SET balance = 20 WHERE id = 1; -- decidiu com saldo velho
COMMIT;
```

Estar dentro de uma transação não impediu a decisão sobre um dado desatualizado. Isso é *read skew* / *lost update*.

### As saídas
- **`SELECT ... FOR UPDATE`**: trava as linhas lidas até o fim da transação. Quem tentar ler com `FOR UPDATE` espera. É o mais comum e o mais barato.
- **`SERIALIZABLE`**: o banco garante que o resultado é equivalente a alguma execução sequencial das transações. Cobre tudo, mas aborta transações com erro de serialização — a aplicação **precisa** tratar e tentar de novo.
- **Guarda no `WHERE`**: em vez de ler-decidir-escrever, escreva a condição na própria escrita e verifique quantas linhas foram afetadas.
  ```SQL
  UPDATE accounts SET balance = balance - 80
  WHERE id = 1 AND balance >= 80;
  ```
  Se afetou 0 linhas, não tinha saldo. Uma operação atômica, sem lock explícito.

### Como ponderar
Comece pelo guard no `WHERE` quando a regra couber numa condição. Use `FOR UPDATE` quando precisar ler, calcular fora do banco e depois escrever. Deixe `SERIALIZABLE` para os poucos fluxos em que a invariante envolve várias linhas ou tabelas.

Conceitos relacionados: [[Transaction]], [[Postgresql]]
