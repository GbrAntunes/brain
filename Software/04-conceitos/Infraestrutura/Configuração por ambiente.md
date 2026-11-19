### Conceito
**Uma imagem, N ambientes.** O código é o mesmo em dev, homologação e prod. O que muda entre eles é só o que é **injetado no container quando ele sobe**: variáveis e segredos. Ambiente novo = conjunto novo de valores, sem mudar código.

### Config é o que muda entre deploys com o mesmo código
Critério (12-factor, fator III): config é o valor que **muda entre deploys enquanto o comportamento é o mesmo**. Teste prático: dava pra tornar o repositório público agora sem vazar nenhuma credencial?

São **dois eixos independentes**:
- **varia por ambiente?** Decide se é config: a chave do Clerk, o bucket do GCS, a `PORT` (o Cloud Run injeta).
- **é segredo?** Decide **onde ela mora**: o bucket é config e não é segredo; a chave do Clerk é as duas coisas.

Casos do social-one-api:
- **Tipo da coluna `timestamp` × `datetime` não é config, é sintoma.** Ele só varia porque dev usa SQLite e prod usa Postgres. Com paridade ([[Paridade entre ambientes]]), a ramificação some. Se o valor muda *porque* o comportamento muda, é o código mal resolvido.
- **`FRONTEND_URL` é config**: não diz "de qual front eu dependo", e sim **quais páginas o navegador pode deixar chamar esta API** (origem do CORS). É uma fronteira por ambiente.

### Uma imagem, promovida, nunca rebuildada
```
commit abc123 → build → imagem :abc123 → homologação (valores de homolog)
                                       → prod        (valores de prod)
```
Um build por ambiente (trigger por branch, cada um com seu `.env`) quebra a garantia da homologação por dois mecanismos:
1. **Mesmo commit ≠ mesmo artefato.** `FROM node:20` aponta pra uma tag que anda, `npm ci` baixa tudo de novo e, principalmente, **o que é diferente no build muda o resultado do build**. A imagem de prod nunca foi testada: a QA aprovou uma irmã dela.
2. **Segredo na imagem vira parte do artefato.** Quem tem pull no Artifact Registry tem as credenciais. O valor continua nas camadas mesmo que um passo seguinte apague o arquivo (`docker history`), e trocar a senha passa a exigir rebuild.

Promovendo uma imagem só, **o que foi testado é byte a byte o que roda**, e a imagem pode circular porque não carrega nada sensível.

Exceção: **SPA com Vite embute as `VITE_*` no bundle durante o build**, porque o navegador não tem variável de ambiente. Pra ter o "build once" no front, a config precisa ser carregada em runtime ([[Build do frontend]]).

### Flag por comportamento, nunca por nome de ambiente
`NODE_ENV` é uma convenção do ecossistema Node. Ele responde **"como otimizar"**, não **"onde estou"**. Quem lê esse valor, conferido no `node_modules`: o Express (ausente vira `development`; fora de `production`, o handler padrão põe o stack trace na resposta) e o npm (com `production`, pula as `devDependencies`). **O runtime do Nest não lê.**

No social-one-api, `NODE_ENV === 'production'` decidia o banco, o tipo da coluna e o Swagger. Criar homologação com `NODE_ENV=homolog` faz ela **cair no ramo de dev e rodar em SQLite**, e ninguém percebe. Adicionar um terceiro ramo também não resolve: cada ambiente novo exige mudar código, e os `if`s se espalham (a lógica do banco já estava duplicada no `seed.ts`).

A correção é a pergunta mudar, de "em que ambiente estou?" pra **"esse comportamento está ligado?"**:

| Antes: `if (NODE_ENV === 'production')` | Depois |
|---|---|
| Postgres × SQLite | **some**: Postgres em todo lugar, endereço via `DATABASE_*` |
| `timestamp` × `datetime` | **some** junto com o SQLite |
| Swagger | `SWAGGER_ENABLED`, decidido por ambiente |

`NODE_ENV=production` vai em **todo ambiente deployado**. O nome do ambiente pode existir (`APP_ENV`, pra log e tag de erro), mas **nenhum `if` decide comportamento por ele**.

### Usar o segredo ≠ ler o segredo
```bash
--set-env-vars=GCS_BUCKET_NAME=social-one-prod   # o valor fica na definição do serviço
--set-secrets=CLERK_SECRET_KEY=clerk-secret-key:latest   # a definição só tem o nome
```
Pro código as duas formas são idênticas (`process.env.CLERK_SECRET_KEY`). A diferença é fora dele:
- **Permissões separadas.** Uma variável comum aparece pra quem pode *ver o serviço* (inclusive nos prints do console). Ler o valor de um segredo exige outra permissão, **segredo por segredo**, dada à service account do serviço. Um dev novo faz deploy referenciando o segredo sem nunca ver o valor.
- **Uma fonte, com versões.** Colado como variável, o valor é copiado em cada serviço e job (API, seed, migration). No Secret Manager, trocar a chave é criar uma versão nova num lugar só.
- O valor é resolvido **quando a instância sobe**: uma versão nova só vale pras instâncias novas.

### Validar no boot transforma erro de config em erro de deploy
Uma variável faltando pode aparecer em três momentos: `getOrThrow` no construtor falha na subida, `process.env.X` solto falha na primeira request que usa o valor, e `?? 'http://localhost:5173'` **nunca falha**, só sobe com valor de dev.

| | Falha no boot | Falha na 1ª request |
|---|---|---|
| Container escuta na `PORT`? | não | **sim** |
| O que o Cloud Run conclui | revisão quebrada, deploy falha | **revisão saudável** |
| Tráfego | continua 100% na anterior | **100% migra pra quebrada** |
| Quem descobre | quem fez o deploy | o cliente, no front |

O Cloud Run só sabe que um container está ruim se ele **não sobe**. Por isso: **um schema único com todas as variáveis**, validado antes do `listen` (`ConfigModule.forRoot({ validate })` no Nest), leitura só via `ConfigService` e **nenhum fallback pra valor que varia por ambiente**.

### Quando usar / quando não usar / sinais de que deu errado
O critério não é o tamanho do app, é **quantos ambientes existem e se existe segredo**:

| Mecanismo | Vale quando |
|---|---|
| Schema validado no boot | existe **qualquer** variável de ambiente: é quase de graça |
| Segredo no Secret Manager | existe segredo e mais alguém vê o console |
| Uma imagem promovida | existe **mais de um ambiente deployado** |
| Flag por comportamento | algum comportamento difere entre ambientes |

Uma LP pessoal, só com prod e sem segredo, não precisa de quase nada disso, além do schema.

Sinais de que deu errado:
- `if` comparando **nome de ambiente** (`NODE_ENV === 'production'`) pra decidir comportamento;
- **fallback com valor de dev** (`?? 'localhost'`);
- **um build por ambiente**: trigger por branch, `--build-arg` com valores, `COPY .env` ou `COPY . .` sem `.dockerignore`;
- `process.env` **espalhado** pelo código, em vez de um módulo único que lê e valida;
- **segredo colado como variável comum**, visível pra quem abre o serviço;
- o mesmo valor **copiado** em vários serviços ou jobs.

### Conceitos relacionados
[[Paridade entre ambientes]], [[Isolamento de ambientes no GCP]], [[Infraestrutura como código]], [[Promoção e rollback no Cloud Run]], [[Build do frontend]], [[CI como gate de deploy]]
