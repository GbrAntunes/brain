### Conceito
A idempotência é a propriedade de uma operação que pode ser aplicada várias vezes sem alterar o resultado final após a primeira execução.

### Exemplo
Chamar um elevador 1 vez ou 35 vezes não altera a ação do elevador. Ele vai responder ao primeiro chamado e todos os próximos ele vai ignorar até que ele conclua a ação do primeiro clique do botão.

### Implementação
Para implementar idempotência em uma aplicação, atribua uma chave à requisição via headers, algo como `X-idempotency-key: '8712318371623'` e aí uma vez que a aplicação que recebe essa requisição identificar que várias requisições com mesmo id chegaram, ele pode trabalhar apenas uma e ignorar o restante.