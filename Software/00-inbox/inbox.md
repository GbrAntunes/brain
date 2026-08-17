A pasta **Inbox** serve para notas rápidas sobre o que precisa ser feito

---

- [ ] [[B-tree]] — contrastar com hash em vez de só citar: hash destrói a ordem, logo não serve pra range, `ORDER BY`, `MIN`/`MAX` nem `LIKE 'abc%'`
- [ ] [[Transaction]] — nível de isolamento: a nota trata isolamento como binário; falta que o default (`READ COMMITTED`) não impede decisão sobre dado velho, e as saídas (`FOR UPDATE`, `SERIALIZABLE`, guarda no `WHERE`)
- [ ] [[Transaction]] — mecanismo da durabilidade: WAL gravado e `fsync` antes do `COMMIT` responder ok; log sequencial vs escrita aleatória nas páginas de dados
- [ ] [[Transaction]] — em "como ponderar": transação curta, sem chamada externa dentro do `BEGIN` (trava linha + segura conexão do pool, e `ROLLBACK` não desfaz efeito fora do banco)
- [ ] [[Idempotência]] — dizer de onde vem a duplicata: retry sobre timeout (resposta perdida, cliente não sabe se processou) e filas *at-least-once*; é o caso estrutural, não o clique duplo
- [ ] [[Idempotência]] — corrigir "ignorar o restante": o certo é guardar a resposta da primeira execução e reproduzi-la na duplicata (mesma requisição → mesma resposta), senão o cliente segue sem feedback
- [ ] [[Idempotência]] — o registro da chave precisa de estado: gravar `in_progress` na chegada com `UNIQUE` no banco (não `SELECT` + `INSERT`, que perde na concorrência) e responder `409` enquanto a primeira não terminou
