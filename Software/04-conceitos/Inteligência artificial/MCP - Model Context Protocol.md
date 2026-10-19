### Conceito
Protocolo aberto, lançado pela Anthropic, que padroniza como aplicações de IA conectam LLMs a ferramentas e dados externos: email, agenda, banco de dados, repositórios, etc.

### O problema: N×M
Sem um padrão, cada aplicação de IA precisa de uma integração própria para cada ferramenta: 5 aplicações × 10 ferramentas = **50 integrações**.

Com MCP, cada aplicação implementa um *client* e cada ferramenta implementa um *server*: 5 + 10 = **15**. A conta deixa de ser multiplicação e vira soma. É a mesma ideia do USB e do LSP.

### Onde está a "interface universal"
O MCP não elimina o contrato: cada tool declara nome, descrição e um JSON Schema dos parâmetros. O que muda é **quem lê o contrato e quando**:

|                    | REST                     | MCP                                                    |
| ------------------ | ------------------------ | ------------------------------------------------------ |
| quem lê o contrato | o dev                    | o modelo                                               |
| quando             | ao programar             | em tempo de execução (`tools/list`)                    |
| resposta           | tipada, campo a campo    | blocos de conteúdo genéricos (texto, imagem, recurso)  |

O protocolo (descobrir ferramentas, chamar, responder) é o mesmo para qualquer servidor, então o client é escrito uma vez só. Analogia: o navegador não tem código para cada site; ele sabe HTTP e HTML.

### O modelo só lida com texto
Tudo o que chega ao modelo vira tokens no contexto.

- **Ida**: o modelo *escreve* a chamada como texto (`{"name": "...", "arguments": {...}}`). Quem faz o parse, valida e executa é o host.
- **Volta**: o host coloca o resultado no contexto, e o modelo o lê como um humano leria. Não existe `response.subject`.

`outputSchema`/`structuredContent` existem para o **host** processar o resultado como código. Para o modelo, continua sendo texto.

### Arquitetura
- **Host**: a aplicação que roda o modelo (ex.: Claude Code). O host **não é o modelo**: é um programa que guarda a conversa, chama a API e encaminha as chamadas de ferramenta.
- **Client**: componente dentro do host que mantém a conexão com **um** servidor (relação 1:1).
- **Server**: programa que expõe tools, resources e prompts.

Os servidores são isolados: nenhum enxerga os outros nem a conversa inteira. Cada um só recebe o que o host decide mandar.

### Primitivas
| Primitiva     | O que é                                       | Quem decide usar              |
| ------------- | --------------------------------------------- | ----------------------------- |
| **Tools**     | ações (enviar email, rodar query)             | o **modelo**                  |
| **Resources** | dados para leitura (arquivo, schema do banco) | a **aplicação/usuário**       |
| **Prompts**   | templates de instrução ("revisar este PR")    | o **usuário** (slash command) |

A divisão **não** é leitura vs escrita: buscar email só lê e mesmo assim é tool. A divisão é **quem toma a iniciativa**. Tool é autônoma, por isso o host costuma pedir confirmação. Com resource, a curadoria do contexto fica com o humano.

`readOnlyHint` e `destructiveHint` são **dicas** declaradas pelo servidor, e o host não tem como verificá-las. A garantia real é o servidor não expor a ação e a credencial dele ter só a permissão necessária (ex.: usuário do Postgres só com `SELECT`).

### Transporte
|              | stdio                                   | Streamable HTTP                                   |
| ------------ | --------------------------------------- | ------------------------------------------------- |
| onde roda    | processo local, iniciado pelo host      | servidor remoto, acessado por URL                 |
| credencial   | na máquina de cada dev                  | centralizada no servidor; usuário entra via OAuth |
| auditoria    | nenhuma central                         | por usuário                                       |
| bom para     | uso pessoal                             | disponibilizar para o time/organização            |

**Cuidado com stdio**: `npx algum-mcp-server` executa código de terceiros na sua máquina, com as suas permissões. É risco de supply chain, como qualquer dependência.

### Conceitos relacionados
[[MCP - Segurança]]
