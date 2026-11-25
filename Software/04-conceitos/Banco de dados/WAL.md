*Write-Ahead Log*: o mecanismo que contribui para a durabilidade de uma [[Transaction]] ser real e ainda assim rápida.

### A regra
Antes de alterar a página de dados, o banco escreve no log **o efeito** da mudança (`balance = 900`), não o comando (`balance = balance - 100`). O `COMMIT` só responde ok depois que esse registro está gravado no disco com `fsync` — não basta estar no buffer do SO, senão uma queda de energia levaria o dado junto.

Valor absoluto dá o mesmo resultado quantas vezes for aplicado; valor relativo, não (o mesmo par absoluto × relativo da [[Idempotência]]). Por isso o recovery pode reaplicar o log sem saber se a página já tinha sido gravada antes da queda.

As páginas de dados em si podem ser escritas bem depois, de forma preguiçosa. Se o banco cair no meio, ele lê o WAL na subida e reaplica o que faltou a partir do último checkpoint (*recovery*).

### Passo-a-passo
1. Recebe a instrução
   O banco de dados recebe a requisição do cliente, por exemplo, um `UPDATE`
2. Modificação em memória
   O banco faz a alteração primeiro nos dados que estão em memória (RAM) e cria um registro detalhado dessa alteração na memória, no buffer do WAL
3. Gravação do log
   Quando o cliente faz o `COMMIT`, o banco grava o registro que havia feito diretamente no disco usando o `fsync()`. Uma vez que o disco confirma a gravação, o processo segue.
4. Sucesso
   Uma vez que o disco confirma a gravação, a transação é considerada commitada oficialmente e o banco de dados responde com a mensagem de sucesso.

### Checkpoint
Evita reaplicar o WAL inteiro no recovery. De tempos em tempos o banco faz flush (com `fsync`) de todas as páginas alteradas em memória até uma posição X do log e anota X. Numa queda, ele sabe exatamente de onde reaplicar (a partir de X), e o log antes de X pode ser reciclado — ou guardado à parte, para backup e *point-in-time recovery*.

### Por que isso é rápido
O WAL é **sequencial**: sempre append no fim do arquivo. As páginas de dados estão espalhadas pelo disco, então gravá-las direto seria **escrita aleatória** — vários pontos distintos, uma por linha alterada.

Trocar N escritas aleatórias por 1 escrita sequencial é a razão de existir do WAL. Em disco mecânico a diferença é de ordens de grandeza; em SSD é menor, mas o ganho de agrupar continua.

### O custo do `fsync`
O WAL existe para garantir a durabilidade: o `COMMIT ok` é a promessa de que o dado existe mesmo após uma queda. Por isso todo commit espera a mídia confirmar a gravação do log — uma latência fixa, não importa o tamanho da transação. Com ~2 ms por ida à mídia, gravando um commit de cada vez, o teto é 500 commits por segundo.

O `write()` sozinho não garante isso: ele responde pela etapa dele, que é entregar o dado ao *page cache* do kernel (RAM). O SO funciona assim porque, se cada `write()` esperasse a mídia, todo programa (editor, compilador, logs) ficaria lento; ele agrupa as escritas e descarrega depois. Só quem prometeu durabilidade paga o `fsync`.

> Nota rápida — *group commit*: commits que chegam juntos dividem um mesmo `fsync`, e o teto deixa de ser um commit por ida à mídia.

### Efeito colateral útil
Como o WAL descreve toda mudança em ordem, ele também serve de base para réplicas e para *point-in-time recovery*: basta a réplica aplicar o mesmo log.

Conceitos relacionados: [[Transaction]], [[Postgresql]]
