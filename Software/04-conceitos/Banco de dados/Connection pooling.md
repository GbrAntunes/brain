### Conceito
Um conjunto de conexões com o banco **abertas uma vez e reaproveitadas**, em vez de abrir e fechar uma por request. Mora do lado da aplicação, não do banco.

### Por que não abrir uma conexão por request
Abrir uma conexão com o [[Postgresql]] não é só abrir um socket:

| Etapa | O que rola |
|---|---|
| Rede | handshake TCP (e TLS, se houver) |
| Autenticação | troca de usuário/senha (SCRAM), mais idas e voltas |
| No servidor | o Postgres **cria um processo do SO** só para essa conexão, com memória própria |
| Aquecimento | o processo começa com cache vazio |

Um request sem pool custa `query + x`, e **x costuma ser maior que a query**: uma query de 1 ms com 5 ms de abertura significa mais de 80% de cerimônia.

O pior, porém, é a concorrência. 500 requests simultâneos viram **500 processos ao mesmo tempo** disputando, por exemplo, 4 núcleos: memória apertada, troca de contexto, e a partir do `max_connections` (padrão **100**) o banco recusa com `too many clients already`. Sem pool, **o pico da API vira, um pra um, pico de conexões no banco**.

### O pool mora na API e tem teto fixo
O ciclo é **pegar → usar → devolver** (sem fechar). O próximo request reaproveita uma conexão já autenticada, com processo e cache quentes, e o `x` some.

No caso comum, o pool vive **na memória do processo da API**, gerenciado pela biblioteca (`pg`, Prisma...). O Postgres nem sabe que ele existe: só vê N conexões que nunca fecham.

Analogia: um prédio com **10 guichês** que a empresa decidiu abrir. Se todos estão ocupados, o cliente entra na fila. Diferente do Cloud Run, que sobe mais instâncias sob carga, o pool **não cresce além do máximo**, e **é esse teto que protege o banco**.

### Pool cheio vira fila, não erro
Com pool de 10 e 500 requests simultâneos, os 490 restantes **esperam numa fila dentro do pool**. Com queries de 1 ms, o pool atende cerca de 10 por ms, e os 500 escoam em **~50 ms**. O pool troca "o banco quebra" por "a API espera um pouco".

A espera tem limite: se passar do timeout de espera do pool, a própria API desiste. São dois erros diferentes:

| Erro | Quem reclama | Significa |
|---|---|---|
| `too many clients already` | o **Postgres** | abriram mais conexões que o `max_connections` |
| timeout esperando conexão do pool | a **API** (biblioteca) | o pool ficou cheio por tempo demais |

### O que esgota o pool é o tempo de posse
**Conexões ocupadas ao mesmo tempo = taxa de requests × tempo que cada um segura a conexão.**

Com 20 req/s e queries de 1 ms: 20 × 0,001 = 0,02 conexões, e um pool de 10 sobra. Se o handler chama um serviço externo de 2 s **segurando a conexão**: 20 × 2 = **40** conexões necessárias, a fila cresce sem parar e os *outros* requests tomam timeout. É o cliente que senta no guichê e fica esperando o motoboy.

O mesmo acontece com:
- **vazamento**: um caminho de erro que pega a conexão e nunca devolve;
- **[[Transaction]] longa**: um `BEGIN` aberto enquanto faz outras coisas.

A correção é **segurar a conexão só durante o trabalho no banco**, e não aumentar o pool. Um timeout apertado só revela o problema mais cedo; não é a causa.

### Pool maior pode ser mais lento
Com 4 núcleos, o banco só executa de fato umas 4 queries por vez. Com pool de 100, os núcleos ficam picotando 100 tarefas, e esse picotar custa: troca de contexto, cache de CPU perdido, disputa por locks e buffers. Cada query fica mais lenta e o total por segundo **cai**.

**A espera acontece de qualquer jeito; o pool só decide onde:**

| | Pool de 10 | Pool de 100 |
|---|---|---|
| Onde se espera | na fila do pool, dentro da API | dentro do Postgres |
| Custo de esperar | quase zero | alto (processo ativo, troca de contexto, locks) |
| Latência | rápida depois que sai da fila | todas lentas ao mesmo tempo |

Ponto de partida citado pela wiki do PostgreSQL e pelo HikariCP: **conexões ≈ núcleos × 2 + discos**. O "× 2" cobre o tempo em que a query espera disco ou rede e outra pode usar o núcleo. Serve para começar; o número certo sai de medir.

### Com várias instâncias, a conta é por instância
Cada instância da API tem **o seu próprio** pool, e nenhum pool sabe dos outros. Com pool de 20 e banco aceitando 60, a 4ª instância já passa do limite: parte das conexões dela recebe `too many clients`.

**pool por instância × máximo de instâncias ≤ max_connections − folga**

- Exemplo: 5 instâncias e pool de 10, totalizando 50 de 60.
- É preciso **travar o máximo de instâncias** (`max-instances` no Cloud Run); senão a conta não vale.
- A **folga** existe porque a API não é a única cliente: o próprio Supabase (auth, storage, realtime), migrations e clientes SQL também abrem conexões.

Essa divisão **não escala**: com 100 instâncias, o pool de cada uma ficaria abaixo de 1. A saída é um único ponto que enxerga o total, o pooler externo (PgBouncer, Supavisor): ver [[Connection pooling - Pooler externo]].

### Conceitos relacionados
[[Postgresql]], [[Transaction]], [[Connection pooling - Pooler externo]]
