Grupo de operações (`INSERT`, `UPDATE`, `DELETE`, etc.) executadas como uma única unidade lógica de trabalho. Ou o sistema executa tudo, ou nada, protegendo a integridade dos dados da base.

### Exemplo de caso de uso
Imagine uma transferência bancária em que precisamos debitar R$100 da Conta A e creditar R$100 na conta B. O que acontece se a operação de crédito der erro após ter debitado da conta A? Para onde vai parar os R$100?
Nesse caso, utilizando transactions, posso amarra-las à uma única transação em que, ou ambas serão executadas sem erro, ou nada será feito.

### ACID
Grupo de conceitos que permitem o uso das transactions
#### Atomicidade
Tudo ou nada. Garante que ou todas as operações da transaction sejam executadas, ou nada feito
#### Consistência
Garante que o banco permaneça em estado válido
#### Isolamento
Impede que uma transação interfira em outra, processando dados como se cada transação fosse executada sozinha. Por exemplo, caso cada usuário altere o mesmo dado, as transações se mantém separadas.

> [!warning] Isolamento não é ligado/desligado
> O default do Postgres é `READ COMMITTED`, que só impede ler dado não commitado — ele **não** impede que você decida com base num dado que ficou velho durante a transação. Ver [[Níveis de isolamento]] para as saídas (`FOR UPDATE`, `SERIALIZABLE`, guarda no `WHERE`).

#### Durabilidade
Garante que uma vez confirmada, a transação seja permanente, mesmo em caso de falhas do sistema.

O mecanismo é o [[WAL]]: antes do `COMMIT` responder ok, o registro do que mudou é gravado no log e passa por `fsync`. As páginas de dados podem ser escritas depois — se cair antes, o log é reaplicado na subida. Funciona porque o log é escrita **sequencial** (append no fim do arquivo), enquanto atualizar as páginas de dados seria escrita **aleatória**, espalhada pelo disco.

### Exemplo
```SQL
BEGIN TRANSACTION; 
	UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
	UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;
	COMMIT;
		BEGIN TRANSACTION;
		UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
		ROLLBACK;
		COMMIT;
```

- **BEGIN**: Marca o início de uma transaction
- **COMMIT**: Finaliza a transação. Após esse comando, tudo que foi executado se torna permanente
- **ROLLBACK**: Desfaz todas as operações, retornando o banco ao estado anterior em caso de erro ou falha

### Como ponderar sobre o uso
- Utilize transações em operações críticas que possuam operações dependentes umas das outras
- Defina mecanismos de tratamento de erros utilizando o **ROLLBACK**
- Caso muitos usuários acessem os mesmos recursos simultaneamente, podemos usar transactions para evitar problemas com concorrência
- **Mantenha a transação curta e nunca coloque chamada externa dentro do `BEGIN`** (HTTP, fila, envio de e-mail). Enquanto a transação está aberta ela segura as linhas travadas e ocupa uma conexão do pool — um gateway lento vira contenção no banco inteiro. Pior: `ROLLBACK` desfaz o que está no banco, mas não desfaz o e-mail já enviado nem a cobrança já feita. Faça a chamada externa **fora** da transação e trate a reentrada com [[Idempotência]]