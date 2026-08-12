Problema de algoritmo em que, tradicionalmente, **buscamos por uma lista (1) e, em seguida, realizamos mais uma busca para cada item dessa lista (+n)**

### Exemplo dos posts dos usuários
Digamos que você queira buscar pelos últimos 5 posts de cada usuário em uma plataforma de leitura.
1. `GET /users` -> retorna 50 usuários
2. `GET /users/1/posts`, `GET /users/2/posts`, ..., `GET /users/50/posts` (50 requisições)

Resultado: para renderizar sua tela, você precisou fazer 51 requisições à API. Imagine 10 mil usuários acessando a plataforma no dia, abrindo a home 15 vezes no dia. **São 4.500.000 requisições em um dia SÓ COM A HOME**

### Soluções
#### ORM
É importante que ao utilizar um ORM, façamos um JOIN dos dados necessários.
Exemplo em TypeORM:
```Typescript
userRepository.find({ relations: ["profile"] })
```

#### Batching
Continuamos fazendo uma busca para trazer a lista e depois, trazemos todos os dados relacionados com os ids da primeira lista.
1. Buscamos a lista de usuários
2. Buscamos todos os posts dos usuários encontrados, algo como:
	1. ```SQL
	   SELECT * FROM posts WHERE user_id IN (1, 2, 3, ...)
	   ```

#### Eager Loading
Se você sabe que vai precisar desses dados, nesse modelo de entidade principal e lista de dados para essa entidade, **carregue-os antecipadamente**

> [!info]
> Nota: por padrão, muitos ORMs usam lazy loading por padrão. Esteja ciente disso ao utilizar esse tipo de ferramenta.
