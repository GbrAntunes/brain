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
- [ ] [[teste de unidade]] — como testar unidade que depende de banco/HTTP: stub via injeção de dependência, e a alternativa melhor (extrair a regra pura, I/O na borda). Distinguir stub/mock/fake/spy
- [x] [[teste de unidade]] — o outro lado das vantagens: o que ele não pega (contrato entre unidades — função devolve centavos, outra espera reais) e o mock desatualizado que fica verde para sempre
- [ ] criar nota [[Partição de equivalência]] (ou seção em teste caixa preta) — critério de parada: classe de equivalência + análise de valor limite, e por que testar `0.1` e `0.37` não acrescenta nada
- [ ] [[Postgresql]] — ligar "objeto-relacional" e "estende o SQL" às features que a nota já cita (tipos e funções próprios, outras linguagens), com exemplos concretos (`CREATE TYPE`, `CREATE FUNCTION`, `ON CONFLICT`)
- [ ] [[Postgresql]] — dizer o que se customiza no [[Teorema PACELC]]: replicação síncrona × assíncrona, e o que cada uma custa
- [x] [[Postgresql]] — link `[[MVVC]]` está com typo (o conceito é MVCC)
- [ ] [[Índices]] — explicar por que FK precisa de índice próprio se a PK do outro lado já tem: direção do JOIN e lookup repetido uma vez por linha do outro lado (Nested Loop)
- [ ] [[Índices]] — dizer por que o índice perde em predicado pouco seletivo (ponteiro até a tabela = acesso aleatório × Seq Scan sequencial) e que seletividade é do valor filtrado, não só da coluna
- [ ] [[Teorema PACELC]] — o exemplo "Postgres = PC/EC" contradiz a regra de que a sigla é da configuração: dizer qual é o padrão de replicação do Postgres e o que se configura pra chegar em EC
- [ ] [[Teorema PACELC]] — o EL hoje só aparece como "mais rápido": falta o que se perde (leitura atrasada na réplica e commit confirmado que some no failover) e a saída intermediária *read-your-writes*
- [ ] [[Teorema CAP]] — explicar quem continua atendendo numa partição CP (o lado com maioria) e por que clusters usam número ímpar de nós, linkando [[Quórum]]
- [ ] [[Transaction]] — a regra "chamada externa fora do `BEGIN`" não mostra a forma: como quebrar o fluxo em transações curtas em volta da chamada, e o que acontece se o processo cair entre elas
- [ ] [[Transaction]] — Consistência diz só "estado válido": falta quem define o que é válido e quem garante (o banco ou a aplicação)
- [ ] [[Transaction]] — o bloco de exemplo tem `BEGIN` aninhado e `COMMIT` depois do `ROLLBACK`; separar em dois exemplos que façam sentido sozinhos
# Sugestões de estudo
---
- [ ] Design Patterns
- [ ] Padrão SAGA
- [ ] [[B-tree]] — o caminho da busca pontual: comparação no nó, descida por um ponteiro, custo logarítmico (árvore rasa e larga) e o salto final da folha pra linha na tabela
- [ ] [[B-tree]] — por que valores só nas folhas favorece range: descida única até o início + caminhada lateral nas folhas encadeadas, sem voltar pra raiz
- [ ] [[B-tree]] — o que acontece numa escrita: inserção localizada na folha e split que pode propagar pra cima (não é "remontar a árvore")
