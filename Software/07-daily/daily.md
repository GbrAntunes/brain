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