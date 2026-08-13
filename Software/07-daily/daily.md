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
