Essa pasta deve ser utilizada para atualizações do dia-a-dia como o que foi feito, o que está pendente, etc.

Exemplo:

2026-08-10
### Hoje Estudei índices no PostgreSQL.
Insight Um índice não é automaticamente bom.
Preciso entender seletividade e o query planner.
### Quero investigar
[[EXPLAIN ANALYZE]]
[[Índice composto]]

## 2026-08-11
- **Tema:** [[Índices]]
- **Como foi:** Seletividade ficou sólida — soube distinguir `status` (2 valores) de `user_id` (alta cardinalidade apesar de repetir), e acertou que não vale índice separado em `status` quando o filtro anterior já cortou quase tudo. Travou no *porquê* da escrita ser mais lenta: modelo mental de "etiquetagem física da memória" em vez de estrutura separada (B-tree) que precisa ser mantida em sincronia a cada escrita.
- **Melhorar nota:** A nota afirma que insert/update/delete ficam mais lentos mas não explica o mecanismo (índice = estrutura à parte; custo escala com a quantidade de índices na tabela). Falta também a palavra "seletividade"/"cardinalidade" nomeada explicitamente, e o motivo de FK indexada (lookup repetido, uma vez por linha do outro lado do JOIN).
## 2026-08-12
- **Tema:** [[Teorema CAP]] + [[Teorema PACELC]]
- **Como foi:** Mandou bem no básico do CAP — identificou sozinho as duas saídas na partição (responder dado velho = A, travar = C) com as letras certas. Travou em dois pontos: (1) achou que "CA" é uma escolha real, tratando P como opcional — não tinha internalizado que partição é evento imposto pela rede, e que "abrir mão de P" já *é* perder C ou A; (2) achou que a sigla PACELC é propriedade do produto, não da configuração — disse que Postgres com réplica assíncrona continuaria PC/EC, quando vira PC/EL. Surgiu dúvida espontânea sobre quórum (R + W > N), que não está em nota nenhuma.
- **Melhorar nota:** CAP apresenta as três combinações como cardápio de igual peso, sem dizer que P é obrigatório em sistema distribuído. PACELC afirma que o `E` existe mas não explica o mecanismo (replicação síncrona vs assíncrona, RTT entre datacenters) nem que a sigla depende da configuração, não do produto.
## 2026-08-13
- **Tema:** [[B-tree]]
- **Como foi:** Acertou o mecanismo da busca pontual — entendeu que a comparação só funciona por causa da ordenação, e que ela descarta uma subárvore inteira, não um valor. No range, captou a proximidade dos valores ("cluster") mas não sabia o que o banco faz depois de achar o início do intervalo (descida única + varredura lateral nas folhas encadeadas da B+tree). Travou em dois pontos: (1) não soube dizer o que hash perde em relação a B-tree — não conectou que hash destrói a ordem, logo não serve pra range, `ORDER BY`, `MIN`/`MAX` nem prefixo de `LIKE`; (2) sobre escrita, disse que a árvore precisa ser "remontada" — modelo mental de rebuild total em vez de inserção localizada na folha, com split ocasional que pode propagar pra cima. A parte de "3 índices = 3 árvores mantidas" saiu depois da correção, fechando o gap registrado em 11/08.
- **Melhorar nota:** A nota é curta e só cobre "para que serve". Falta a estrutura (nós com várias chaves, árvore rasa e larga, custo logarítmico), o mecanismo do range (B+tree, folhas encadeadas) e o contraste real com hash — hoje ele aparece como nome solto no fim.
## 2026-08-14
- **Tema:** [[Transaction]]
- **Como foi:** Melhor momento foi a durabilidade — derivou o WAL sozinho ("registra os comandos aprovados e refaz no boot"), só faltou o nome e o ajuste de que o log guarda o efeito resolvido, não o SQL a reexecutar. Na 4 pegou a intuição certa de recurso preso por transação longa. Travou em dois pontos: (1) na pergunta de abertura foi direto pra concorrência/"ordem dos comandos se misturar" e passou reto pela atomicidade — não viu que o esquema compensatório depende da aplicação continuar viva, e que a transaction transfere o rollback pro banco; (2) disse que a transaction protegeria do estoque negativo com dois `SELECT` concorrentes, confiando na frase idealizada da nota ("como se cada transação fosse executada sozinha") — não tinha o conceito de nível de isolamento. Ficou claro que atomicidade só vale dentro do banco: `ROLLBACK` não desfaz cobrança de gateway. Ganchos abertos: [[Idempotência]] (nota existe, nunca estudada) e níveis de isolamento.
- **Melhorar nota:** Isolamento aparece como binário, sem a palavra "nível de isolamento" nem o default `READ COMMITTED`. Durabilidade afirma "permanente mesmo em falha" sem o mecanismo (WAL + `fsync` antes do ok, sequencial vs escrita aleatória). "Como ponderar sobre o uso" não avisa pra manter a transação curta nem pra deixar chamada externa fora do `BEGIN`.
- **Obs:** `04-conceitos/` tem subpastas (Algoritmos, Banco de dados, Testes) — 14 notas, não 2. Nunca estudadas: Idempotência, Problema n+1, EXPLAIN ANALYZE, Postgresql, SGBD, Índice composto, Testes de software, teste de unidade, Sistemas distribuídos.
- **Nota rasa:** [[Sistemas distribuídos]] tem 3 linhas e nenhum link — não dá pra testar; vale expandir e pendurar CAP/PACELC/Quórum nela.
