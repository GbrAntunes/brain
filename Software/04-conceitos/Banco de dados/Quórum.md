Número mínimo de réplicas necessárias para que uma operação distribuída seja concluída antes de se declarar a operação como bem-sucedida. O quórum garante consistência necessária para operações distribuídas.

Em um [[Cluster]] contendo 5 réplicas, ao menos 3 precisam responder com sucesso para que uma operação enviada para esse cluster possa responder de forma positiva.
### Cálculo padrão de quórum
`Q = [N/2] + 1`

Ou seja, o mínimo de réplicas que respondem "ok" para uma operação, precisa ser de pelo menos mais da metade do total de réplicas.

Mas nem sempre é assim

### Quórum configurável

`R + W > N`

onde:
`R`: Quantas réplicas são necessárias para uma operação de leitura
`W`: Quantas réplicas são necessárias para uma operação de escrita
`N`: Número total de réplicas

