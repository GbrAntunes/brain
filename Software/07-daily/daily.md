Essa pasta deve ser utilizada para atualizações do dia-a-dia como o que foi feito, o que está pendente, etc.

Exemplo:

2026-08-10
### Hoje Estudei índices no PostgreSQL.
Insight Um índice não é automaticamente bom.
Preciso entender seletividade e o query planner.
### Quero investigar
[[EXPLAIN ANALYZE]]
[[Composite Index]]

## 2026-08-11
- **Tema:** [[Índices]]
- **Como foi:** Seletividade ficou sólida — soube distinguir `status` (2 valores) de `user_id` (alta cardinalidade apesar de repetir), e acertou que não vale índice separado em `status` quando o filtro anterior já cortou quase tudo. Travou no *porquê* da escrita ser mais lenta: modelo mental de "etiquetagem física da memória" em vez de estrutura separada (B-tree) que precisa ser mantida em sincronia a cada escrita.
- **Melhorar nota:** A nota afirma que insert/update/delete ficam mais lentos mas não explica o mecanismo (índice = estrutura à parte; custo escala com a quantidade de índices na tabela). Falta também a palavra "seletividade"/"cardinalidade" nomeada explicitamente, e o motivo de FK indexada (lookup repetido, uma vez por linha do outro lado do JOIN).
## 2026-08-12
- **Tema:** [[Teorema CAP]] + [[Teorema PACELC]]
- **Como foi:** Mandou bem no básico do CAP — identificou sozinho as duas saídas na partição (responder dado velho = A, travar = C) com as letras certas. Travou em dois pontos: (1) achou que "CA" é uma escolha real, tratando P como opcional — não tinha internalizado que partição é evento imposto pela rede, e que "abrir mão de P" já *é* perder C ou A; (2) achou que a sigla PACELC é propriedade do produto, não da configuração — disse que Postgres com réplica assíncrona continuaria PC/EC, quando vira PC/EL. Surgiu dúvida espontânea sobre quórum (R + W > N), que não está em nota nenhuma.
- **Melhorar nota:** CAP apresenta as três combinações como cardápio de igual peso, sem dizer que P é obrigatório em sistema distribuído. PACELC afirma que o `E` existe mas não explica o mecanismo (replicação síncrona vs assíncrona, RTT entre datacenters) nem que a sigla depende da configuração, não do produto.
