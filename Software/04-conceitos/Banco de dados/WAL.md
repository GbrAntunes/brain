*Write-Ahead Log*: o mecanismo que faz a durabilidade de uma [[Transaction]] ser real e ainda assim rápida.

### A regra
Antes de alterar a página de dados, o banco escreve no log **o que vai fazer**. O `COMMIT` só responde ok depois que esse registro está gravado no disco com `fsync` — não basta estar no buffer do SO, senão uma queda de energia levaria o dado junto.

As páginas de dados em si podem ser escritas bem depois, de forma preguiçosa. Se o banco cair no meio, ele lê o WAL na subida e reaplica o que faltou (*recovery*).

### Por que isso é rápido
O WAL é **sequencial**: sempre append no fim do arquivo. As páginas de dados estão espalhadas pelo disco, então gravá-las direto seria **escrita aleatória** — vários pontos distintos, uma por linha alterada.

Trocar N escritas aleatórias por 1 escrita sequencial é a razão de existir do WAL. Em disco mecânico a diferença é de ordens de grandeza; em SSD é menor, mas o ganho de agrupar continua.

### Efeito colateral útil
Como o WAL descreve toda mudança em ordem, ele também serve de base para réplicas e para *point-in-time recovery*: basta a réplica aplicar o mesmo log.

Conceitos relacionados: [[Transaction]], [[Postgresql]]
