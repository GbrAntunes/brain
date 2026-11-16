### Conceito
Quando API e front vivem em repos separados (`projeto/projeto-api`, `projeto/projeto-web`), o ambiente local que sobe tudo junto precisa morar em **algum lugar versionado**. Um `docker-compose` na pasta pai, fora de qualquer repo, não é de ninguém.

### As quatro saídas

| Saída | Como fica | Custo |
|---|---|---|
| **Compose no repo da API** | build context `../projeto-web` | acopla a API ao layout de pastas; quem só mexe no front precisa clonar a API |
| **Repo de orquestração** (`projeto-dev`) | terceiro repo com compose, scripts de clone, seed e futura IaC | mais um repo; nada garante que as versões locais da API e do front combinam |
| **Monorepo** | um repo, `api/` e `web/`, compose na raiz | migração; o CI precisa saber qual pasta mudou (o Cloud Build filtra triggers por caminho) |
| **Submodules** | repo dentro de repo | frágil no dia a dia |

O compose não precisa containerizar tudo. Para [[Paridade entre ambientes]], o que importa são os **serviços de apoio iguais aos de prod** (Postgres na mesma versão do Supabase); o front pode seguir no Vite com hot reload.

### Monorepo junta a mudança, não o deploy
Mudar um endpoint e a tela que o consome é, em repos separados, dois commits, duas PRs e dois deploys. O monorepo junta **commit e PR**: a mudança é revisada como unidade e, localmente, API e front estão sempre na mesma versão.

Mas os **deploys continuam dois** (Cloud Run para a API, outro destino para o front), e no intervalo entre eles roda front velho com API nova ou o contrário. Quem resolve esse intervalo não é a organização dos repos, é **mudança compatível**: o novo convive com o antigo até o front migrar ([[Mudança compatível de API]]).

### Critério

| Situação | Escolha |
|---|---|
| mesmo time nos dois lados, mudanças atravessando API e front com frequência | **monorepo** |
| API com outros consumidores (mobile, parceiros) ou times/ritmos de release diferentes | **repos separados + repo de orquestração** |

### Conceitos relacionados
[[Paridade entre ambientes]], [[Mudança compatível de API]], [[CI como gate de deploy]]
