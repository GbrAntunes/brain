A pasta **Inbox** serve para notas rápidas sobre o que precisa ser feito

---
# Melhorias

- [ ] [[Níveis de isolamento]] — nomear *dirty read* como a única garantia do `READ COMMITTED`
- [ ] [[Níveis de isolamento]] — incluir `REPEATABLE READ`; a nota promete "níveis" mas só mostra dois
- [ ] [[Níveis de isolamento]] — ligar o retry do `SERIALIZABLE` a [[Idempotência]]
- [ ] [[WAL]] — explicar o checkpoint: como o recovery sabe de onde reaplicar
- [ ] [[WAL]] — replicação síncrona vs assíncrona (commit esperando o ack da réplica) e link pra [[Teorema PACELC]]
- [ ] [[WAL]] — o custo do `fsync`: latência por commit, e por que o buffer do SO retorna sucesso antes da mídia
- [ ] [[lost update]] — completar "sistema de versões": o `UPDATE` com a versão no `WHERE`, a checagem de rowcount e o retry relendo (e tirar a frase de versão da seção de lock pessimista)
- [ ] [[lost update]] — no lock pessimista, dizer o que B lê quando a trava é liberada e que `FOR UPDATE` não bloqueia `SELECT` comum
- [ ] [[lost update]] — critério de escolha entre as três: quando o cálculo cabe no SQL, quando há chamada externa, frequência de conflito
- [ ] [[Índice composto]] — deixar claro que a ordem que importa é a da definição do índice, não a do `WHERE`
- [ ] [[Índice composto]] — critério pra escolher a ordem das colunas (consultas a atender + seletividade da 1ª coluna), com link pra [[Índices]]
- [ ] [[Quórum]] — a leitura volta com `R` respostas que podem divergir; é a versão/timestamp que escolhe a atual (mesmo mecanismo da coluna `version` de [[lost update]])
- [ ] [[Quórum]] — critério pra desbalancear `R` e `W` (número pequeno pra operação frequente) e o custo de `W = N`: escrita para se uma réplica cai, latência refém da mais lenta — linkar [[Teorema PACELC]]
- [ ] [[Cluster]] — expandir além da definição: o que as réplicas resolvem, como decidem quem responde, e linkar [[Quórum]] e [[Sistemas distribuídos]]
- [ ] [[EXPLAIN ANALYZE]] — incluir `Rows Removed by Filter`: é ele que diz se vale índice (ler 1 mi e devolver 3 vs devolver 400 mil), linkando a seletividade de [[Índices]]
- [ ] [[EXPLAIN ANALYZE]] — separar `ANALYZE` (comando que recoleta estatísticas) de `EXPLAIN ANALYZE`; estatística velha é a causa usual de estimado ≠ real. Citar `BEGIN; ... ROLLBACK;` como forma de medir DML sem persistir ([[Transaction]])
- [ ] [[Testes de software]] — preencher "Pirâmide de teste" e "Cobertura" (hoje só títulos): proporção entre os níveis e o que a cobertura mede/não garante
- [ ] [[Testes de software]] — trade-off de custo manual × automatizado (custo de escrita × custo por execução)
# Sugestões de estudo
---
- [ ] Design Patterns
- [ ] Padrão SAGA
