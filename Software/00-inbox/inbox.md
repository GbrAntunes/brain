A pasta **Inbox** serve para notas rápidas sobre o que precisa ser feito

---
# Melhorias

- [x] Adicionar conceito de [[lost update]] 
- [ ] [[Níveis de isolamento]] — nomear *dirty read* como a única garantia do `READ COMMITTED`
- [ ] [[Níveis de isolamento]] — incluir `REPEATABLE READ`; a nota promete "níveis" mas só mostra dois
- [ ] [[Níveis de isolamento]] — ligar o retry do `SERIALIZABLE` a [[Idempotência]]
- [ ] [[WAL]] — explicar o checkpoint: como o recovery sabe de onde reaplicar
- [ ] [[WAL]] — replicação síncrona vs assíncrona (commit esperando o ack da réplica) e link pra [[Teorema PACELC]]
- [ ] [[WAL]] — o custo do `fsync`: latência por commit, e por que o buffer do SO retorna sucesso antes da mídia
- [ ] [[lost update]] — completar "sistema de versões": o `UPDATE` com a versão no `WHERE`, a checagem de rowcount e o retry relendo (e tirar a frase de versão da seção de lock pessimista)
- [ ] [[lost update]] — no lock pessimista, dizer o que B lê quando a trava é liberada e que `FOR UPDATE` não bloqueia `SELECT` comum
- [ ] [[lost update]] — critério de escolha entre as três: quando o cálculo cabe no SQL, quando há chamada externa, frequência de conflito
- [ ] [[Índice composto]] — explicar o porquê da ordem: ordenado pela 1ª coluna, 2ª só dentro de cada valor da 1ª (prefixo à esquerda)
- [ ] [[Índice composto]] — deixar claro que a ordem que importa é a da definição do índice, não a do `WHERE`
- [ ] [[Índice composto]] — critério pra escolher a ordem das colunas (consultas a atender + seletividade da 1ª coluna), com link pra [[Índices]]
- [ ] [[Quórum]] — explicar *por que* `R + W > N` funciona: sobreposição garantida entre o conjunto escrito e o lido, e por que `>=` não basta (N=4, W=2, R=2 admite conjuntos disjuntos)
- [ ] [[Quórum]] — a leitura volta com `R` respostas que podem divergir; é a versão/timestamp que escolhe a atual (mesmo mecanismo da coluna `version` de [[lost update]])
- [ ] [[Quórum]] — critério pra desbalancear `R` e `W` (número pequeno pra operação frequente) e o custo de `W = N`: escrita para se uma réplica cai, latência refém da mais lenta — linkar [[Teorema PACELC]]
# Sugestões de estudo
---
- [ ] Design Patterns
- [ ] Padrão SAGA
