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
# Sugestões de estudo
---
- [ ] Design Patterns
- [ ] Padrão SAGA
