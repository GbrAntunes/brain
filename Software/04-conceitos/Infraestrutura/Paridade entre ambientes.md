### Conceito
Dev, homologação e prod devem ser **iguais na forma e diferentes na identidade**: o mesmo código, stack e desenho de infra, mas cada um com seus próprios dados, credenciais e serviços externos.

### Iguais na forma, diferentes na identidade

| Tem que ser **igual** (paridade)                  | Tem que ser **diferente** (isolamento)                                          |
| ------------------------------------------------- | ------------------------------------------------------------------------------- |
| o código, de preferência a mesma imagem           | os **dados**                                                                    |
| engine e versão do banco                          | as **credenciais** e segredos                                                   |
| schema e migrations                               | os **valores** da config (qual banco, qual bucket)                              |
| o formato da config (as mesmas variáveis existem) | as instâncias de serviços externos (Clerk de dev × de prod, gateway em sandbox) |
| o desenho da infra                                | tamanho e escala                                                                |

Espelhar um ambiente é espelhar a **forma**, nunca a identidade. Homologação com "as mesmas configurações" de prod aponta pro banco de prod.

### Paridade é gradiente, isolamento é binário
Cada coluna falha de um jeito:

- **SQLite em dev, Postgres em prod (falha de paridade)**: o que devia ser igual está diferente. Além do remendo no código para tipos que o SQLite não tem, **um comportamento que não existe em dev não pode ser testado em dev**: o SQLite aceita `"abc"` numa coluna `INTEGER`, e lock por linha e [[Níveis de isolamento]] não funcionam igual, então bug de concorrência ([[lost update]]) não reproduz localmente.
- **Portal com dev apontando pro Supabase do cliente (falha de isolamento)**: o que devia ser diferente estava igual, e era **a credencial**. Código inacabado (um sync ativo) rodava com permissão de escrita sobre dados reais de um cliente sensível: usuários estranhos, alterações suspeitas, risco de apagar ou vazar dado pessoal (LGPD).

| | Falha de paridade | Falha de isolamento |
|---|---|---|
| Quem sofre | o time (bug tardio, remendo) | **o cliente** |
| Dá pra remediar? | sim: corrige e faz deploy de novo | às vezes não: dado apagado ou vazado |
| Natureza | **gradiente**, nunca perfeita | **binária**: dev alcança prod ou não |

Paridade se aproxima o quanto der; **isolamento é inegociável**.

### Deploy rápido só é seguro com as três distâncias fechadas
O 12-factor (fator X) diz que dev e prod se afastam por três distâncias: **tempo** (quanto o código leva pra chegar em prod), **pessoas** (quem escreve × quem faz o deploy) e **ferramentas** (stack diferente).

O fluxo push na `main` → Cloud Build → prod fecha tempo e pessoas, mas deixa ferramentas aberta. Aí o teste local **não prova nada sobre prod**, e a velocidade joga contra: **o bug chega no usuário em minutos**. Fechar uma distância sem as outras não reduz o risco, só acelera a chegada dele.

Para deploy rápido ser seguro, o código precisa ter sido validado num ambiente espelhado, e os critérios de qualidade precisam ser **automáticos** ([[CI como gate de deploy]]): critério manual atrasa (reabre a distância de tempo) ou é pulado "só dessa vez".

### Isolamento por construção, não por disciplina
No portal, a proteção era **alguém lembrar** de trocar a URL do banco. Isso falha na primeira pressa, no dev novo, no `.env` copiado. Por construção, dev **não consegue** alcançar prod:
- a credencial de prod não existe fora de prod (Secret Manager, lida só pelo service account de prod);
- cada ambiente tem identidades próprias: service account, projeto do Supabase, instância do Clerk;
- o erro vira **`403`**, não lixo silencioso no banco do cliente.

Contraexemplo real: um passo a passo que manda cada dev gerar uma **chave JSON do service account da aplicação**. A identidade não tem dono (a auditoria mostra a SA, não a pessoa), há uma cópia da chave por laptop, a chave não expira por padrão e, se for a mesma SA do Cloud Run, **cada laptop carrega a identidade de prod**. Melhor: SA por ambiente, cada dev com a própria conta (`gcloud auth application-default login`) com acesso só a dev, e SA de prod sem chave. Detalhes em [[Isolamento de ambientes no GCP]].

É o mesmo princípio do mínimo privilégio em outro eixo: na rota, o padrão é exigir o nível mais alto e abrir é explícito; no ambiente, o padrão é não alcançar nada fora de si. Nos dois, **o padrão mora na infraestrutura, não na memória de quem escreve**.

### Homologação testa o artefato que vai pra prod

| | Dev | Homologação | Prod |
|---|---|---|---|
| De quem | de cada dev | compartilhado (QA, segurança, cliente) | usuários |
| Estabilidade | muda a todo minuto | só muda quando uma versão é promovida | estável |
| O que roda | o código da máquina | **a mesma imagem que vai pra prod** | a imagem aprovada |
| Dados | descartáveis | controlados e resetáveis ([[Dados de homologação]]) | reais |

- Pentest em dev testa uma versão que não vai pra prod: o resultado não vale pra release.
- Serviços externos em **sandbox**: senão o pentest que cria 500 usuários dispara 500 e-mails reais.
- **QA e segurança no mesmo ambiente se contaminam**: depois do pentest, a QA testa em cima de dados deturpados com cara de legítimos, e o resultado não prova nada. Saídas: **janela + reset** (mais simples, ponto de partida) ou **ambiente efêmero** só pro pentest, que exige ambiente reproduzível por código ([[Infraestrutura como código]]).
- Regra: **ambiente por propósito, com dono e prazo**, não ambiente permanente por pessoa. Cada ambiente permanente a mais é mais uma coisa pra manter em paridade.

### Conceitos relacionados
[[Dados de homologação]], [[Ambiente local com múltiplos repositórios]], [[Isolamento de ambientes no GCP]], [[Configuração por ambiente]], [[CI como gate de deploy]], [[Infraestrutura como código]], [[Postgresql]], [[Níveis de isolamento]]
