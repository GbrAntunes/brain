*Write-Ahead Log*: o mecanismo que faz a durabilidade de uma [[Transaction]] ser real e ainda assim rápida.

### A regra
Antes de alterar a página de dados, o banco escreve no log **o que vai fazer**. O `COMMIT` só responde ok depois que esse registro está gravado no disco com `fsync` — não basta estar no buffer do SO, senão uma queda de energia levaria o dado junto.

As páginas de dados em si podem ser escritas bem depois, de forma preguiçosa. Se o banco cair no meio, ele lê o WAL na subida e reaplica o que faltou (*recovery*).

### Passo-a-passo
1. Recebe a instrução
   O banco de dados recebe a requisição do cliente, por exemplo, um `UPDATE`
2. Modificação em memória
   O banco faz a alteração primeiro nos dados que estão em memória (RAM) e cria um registro detalhado dessa alteração na memória, no buffer do WAL
3. Gravação do log
   Quando o cliente faz o `COMMIT`, o banco grava o registro que havia feito diretamente no disco usando o `fsync()`. Uma vez que o disco confirma a gravação, o processo segue.
4. Sucesso
   Uma vez que o disco confirma a gravação, a transação é considerada commitada oficialmente e o banco de dados responde com a mensagem de sucesso.
### Por que isso é rápido
O WAL é **sequencial**: sempre append no fim do arquivo. As páginas de dados estão espalhadas pelo disco, então gravá-las direto seria **escrita aleatória** — vários pontos distintos, uma por linha alterada.

Trocar N escritas aleatórias por 1 escrita sequencial é a razão de existir do WAL. Em disco mecânico a diferença é de ordens de grandeza; em SSD é menor, mas o ganho de agrupar continua.

### Efeito colateral útil
Como o WAL descreve toda mudança em ordem, ele também serve de base para réplicas e para *point-in-time recovery*: basta a réplica aplicar o mesmo log.

Conceitos relacionados: [[Transaction]], [[Postgresql]]
