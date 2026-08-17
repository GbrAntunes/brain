Os índices são estruturas de dados auxiliares que servem para que um SGBD não precise varrer todo a base até encontrar o dado que precisa. Pode ser tanto um índice simples, quanto um [[Índice composto]]. Por exemplo, em um dicionário, se você está procurando pela palavra "dados", você pode olhar na lateral do livro e ver onde estão as palavras que começam com "d" e aí você começa a procurar por aí. Além disso, você sabe que o dicionário está organizado em ordem alfabética e - portanto - a palavra "dados" deve estar no início das palavras que começam com "d". Graças a essa organização você não precisou procurar nas palavras que começam com "a", "b" e "c".

Ao invés do banco fazer uma varredura completa na base (full table scan), o banco usa o índice para achar o caminho exato do dado no disco quase instantaneamente.

### Exemplo prático
Existe uma tabela `users` contendo um milhão de linhas. Essa tabela conta com os seguintes atributos:

| atributo | UN  | PK  |
| -------- | --- | --- |
| id       | n/a | sim |
| name     | n   | não |
| email    | s   | não |
| status   | n   | não |

Caso eu queira buscar por um usuário, posso criar um índice para email, já que é um dado único por usuário e serve bem para buscar por um usuário em específico. Para implementar esse índice em um banco de dados Postgres, posso usar o seguinte comando:

```SQL
CREATE UNIQUE INDEX idx_usuarios_email ON users(email);
```


> [!PK]
> 💡Geralmente, PK serão índices (no caso do Postgres, isso ocorre de forma automática)

### Como escolher um bom índice
O índice deve ser escolhido com base no comportamento da aplicação. A depender de como sua aplicação vai buscar pelos dados, você escolhe os índices. Se você criar um índice para `status` que provavelmente é um ENUM `active | inactive` o banco ainda vai precisar varrer 80% da base e provavelmente nem vai usar o índice por perceber que fazer o full table scan é mais rápido.

#### Sobre cardinalidade e seletividade
Por que índice em FK e não em status? Status com ENUM `active | inactive` é cardinalidade 2; email é cardinalidade 1 milhão.
**Um índice só compensa quando o predicado elimina a grande maioria das linhas.**

### Como considerar o uso de índices
- Usar índices torna a **leitura** (select) mais rápida;
- Usar índices torna a **alteração dos dados** (insert, update, delete) mais lenta;
- Colunas usadas frequentemente nos seus `where` são bons candidatos a serem indexados
- Colunas de ligação como `FK` devem quase sempre, serem indexadas para `JOIN` mais rápidos
- Tabelas que são sempre usadas com uma ordenação default (como ordenado por nomes ou dados mais frequentes) podem indexar os atributos utilizados para a ordenação
- Caso a entidade sofra consideravelmente mais operações de leitura do que de alteração de dados, os índices são bem-vindos pois **tornam a busca por informações mais rápidas, mas as alterações de dados mais lentas**. Isso ocorre pois índices são salvos em uma estrutura de dados à parte (no caso do Postgres, [[B-tree]]) e alterar dados envolve alterar também essa estrutura de dados.