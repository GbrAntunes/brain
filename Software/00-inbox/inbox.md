A pasta **Inbox** serve para notas rápidas sobre o que precisa ser feito

- [x] [[Índices]] — explicar *por que* a escrita fica mais lenta: o índice é uma estrutura separada (B-tree), e o custo escala com a quantidade de índices da tabela
- [ ] [[Índices]] — nomear "seletividade"/"cardinalidade" explicitamente; é o conceito que separa o caso do `status` do caso da `FK`
- [ ] criar nota [[Índice composto]] — quando um índice `(a, b)` substitui dois índices separados
- [x] [[Teorema CAP]] — dizer que P não é opcional em sistema distribuído: a escolha real é CP ou AP, e "CA" só descreve um nó só
- [x] [[Teorema PACELC]] — explicar o mecanismo do `E`: replicação síncrona (EC) vs assíncrona (EL), e que a sigla é da *configuração*, não do produto (Postgres com réplica async = PC/EL)
- [ ] criar nota [[Quórum]] — a regra `R + W > N` e como mexer em W/R desliza o sistema entre EC e EL
