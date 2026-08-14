A pasta **Inbox** serve para notas rápidas sobre o que precisa ser feito

- [x] [[Índices]] — explicar *por que* a escrita fica mais lenta: o índice é uma estrutura separada (B-tree), e o custo escala com a quantidade de índices da tabela
- [ ] [[Índices]] — nomear "seletividade"/"cardinalidade" explicitamente; é o conceito que separa o caso do `status` do caso da `FK`
- [ ] criar nota [[Índice composto]] — quando um índice `(a, b)` substitui dois índices separados
- [x] [[Teorema CAP]] — dizer que P não é opcional em sistema distribuído: a escolha real é CP ou AP, e "CA" só descreve um nó só
- [x] [[Teorema PACELC]] — explicar o mecanismo do `E`: replicação síncrona (EC) vs assíncrona (EL), e que a sigla é da *configuração*, não do produto (Postgres com réplica async = PC/EL)
- [ ] criar nota [[Quórum]] — a regra `R + W > N` e como mexer em W/R desliza o sistema entre EC e EL
- [ ] [[B-tree]] — explicar o range: os bancos usam B+tree, dados nas folhas e folhas encadeadas, então é uma descida só + varredura lateral até sair do intervalo
- [ ] [[B-tree]] — contrastar com hash em vez de só citar: hash destrói a ordem, logo não serve pra range, `ORDER BY`, `MIN`/`MAX` nem `LIKE 'abc%'`
- [ ] [[B-tree]] — descrever a estrutura: cada nó tem várias chaves (não é binária), árvore rasa e larga, busca logarítmica; e a escrita é inserção localizada na folha + split ocasional, não rebuild
- [ ] [[Transaction]] — nível de isolamento: a nota trata isolamento como binário; falta que o default (`READ COMMITTED`) não impede decisão sobre dado velho, e as saídas (`FOR UPDATE`, `SERIALIZABLE`, guarda no `WHERE`)
- [ ] [[Transaction]] — mecanismo da durabilidade: WAL gravado e `fsync` antes do `COMMIT` responder ok; log sequencial vs escrita aleatória nas páginas de dados
- [ ] [[Transaction]] — em "como ponderar": transação curta, sem chamada externa dentro do `BEGIN` (trava linha + segura conexão do pool, e `ROLLBACK` não desfaz efeito fora do banco)
