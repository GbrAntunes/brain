Para entender o custo de uma query, podemos analisar dois dados importantes: o plano de execução e o custo da execução.
Para isso, [[SGBD]] como o [[Postgresql]] dispõem de dois comandos importantes: `EXPLAIN` e `ANALYZE`.

### EXPLAIN
O Explain exibe um plano de execução para a query informada

```SQL
EXPLAIN
SELECT *
	FROM orders
	WHERE customer_email = 'gabriel@email.com';
```
```SQL
Seq Scan on orders Filter: (customer_email = 'gabriel@email.com')
```

- **Seq Scan**: Sequential Scan - vai percorrer a tabela

### ANALYZE
O Analyze é usado juntamente com o `EXPLAIN` e a principal diferença é que ele efetivamente executa a query, não apenas a planeja. Executando a query temos algumas informações importantíssimas adicionais referentes ao **custo da query**

```SQL
EXPLAIN ANALYZE
SELECT *
	FROM orders
	WHERE customer_email = 'gabriel@email.com';
```
```SQL
Index Scan using idx_orders_customer_email on orders
  (cost=0.42..8.44 rows=1 width=128)
  (actual time=0.031..0.032 rows=1 loops=1)
```

- **cost**: estimativa do Postgres sobre o custo daquele plano (não é ms, é uma unidade própria da engine do Postgres)
- **actual time**: É o custo real da execução em ms, significando que a primeira linha ficou disponível em 0.031ms e o resultado total em 0.032ms

**IMPORTANTE**: lembre-se que o `ANALYZE` executa de fato a query, portanto, tenha cuidado ao analisar operações com `INSERT`, `UPDATE` e `DELETE`

### Utilidade
Essas ferramentas são extremamente úteis para trabalharmos com [[Índices]]. Uma vez que temos uma desconfiança de uma query que poderia se beneficiar de [[Índices]], podemos rodar esses testes e verificar se o custo daquela query justifica de fato o uso de [[Índices]]. Caso aquela query seja bastante utilizada mas o custo é baixo, não faz sentido indexar aqueles atributos.