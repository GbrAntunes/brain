### Conceito
A idempotência é a propriedade de uma operação que pode ser aplicada várias vezes sem alterar o resultado final após a primeira execução.

### Exemplo
Chamar um elevador 1 vez ou 35 vezes não altera a ação do elevador. Ele vai responder ao primeiro chamado e todos os próximos ele vai ignorar até que ele conclua a ação do primeiro clique do botão.

### De onde vem a duplicata
O clique duplo do usuário é o caso anedótico. O caso estrutural é outro:

- **Retry sobre timeout**: o cliente envia, o servidor processa, a resposta se perde na volta. O cliente não tem como distinguir "não processou" de "processou e eu não soube" — então ele tenta de novo, e está certo em tentar.
- **Filas *at-least-once*** (SQS, Kafka, RabbitMQ): a entrega duplicada é garantia do broker, não bug. Se o consumer não confirma a tempo, a mensagem volta pra fila.

Ou seja: a duplicata é inevitável em sistema distribuído. Idempotência é o que torna ela inofensiva.

### Implementação
Atribua uma chave à requisição via header, algo como `X-Idempotency-Key: '8712318371623'`, gerada pelo **cliente** (o servidor não sabe que duas requisições são a mesma).

#### 1. A resposta é reproduzida, não ignorada
O erro comum é "processa a primeira e ignora o resto". Se a duplicata veio de um retry por timeout, o cliente **ainda não tem a resposta** — ignorar deixa ele no escuro pra sempre.

O correto: guardar a resposta da primeira execução junto com a chave e **reproduzi-la** na duplicata. Mesma requisição → mesma resposta, mesmo status.

#### 2. A chave precisa de estado, não só de existência
Registrar a chave só no fim não resolve: duas requisições concorrentes passam pela verificação antes de qualquer uma terminar. Grave o registro **na chegada**, com estado:

| estado | significado | resposta à duplicata |
| ------ | ----------- | -------------------- |
| `in_progress` | primeira ainda executando | `409 Conflict` — tente de novo depois |
| `completed` | terminou | resposta guardada, replay |

```SQL
CREATE TABLE idempotency_keys (
  key         TEXT PRIMARY KEY,   -- UNIQUE é o que resolve a corrida
  status      TEXT NOT NULL,
  response    JSONB,
  created_at  TIMESTAMPTZ DEFAULT now()
);
```

**O `UNIQUE` do banco é quem decide o vencedor**, não a aplicação. `SELECT` + `INSERT` é uma janela de corrida: entre um e outro, a outra requisição também não encontrou nada e também vai inserir. Faça o `INSERT` direto e trate a violação de constraint como "já existe".

### Conceitos relacionados
[[Transaction]], [[Sistemas distribuídos]]