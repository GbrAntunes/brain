Tipo de [[Índices]] que associa duas ou mais colunas de uma tabela em uma única chave de indexação, útil quando uma busca, ordenação e filtro utilizam esses campos de forma combinada.

**A ordem do índice composto importa**. Portanto, no exemplo a seguir, o índice `(department_id, role)` considera primeiro `department_id` e depois `role`. Para filtros por departamento ou por departamento + role, é um bom índice. Para um filtro por role, esse índice já não é interessante.

Então se eu busco por todos os devs do departamento 10, o banco de dados vai primeiro encontrar o departamento 10 e nos resultados encontrados, busca pelos devs
### Tabela exemplo
|  id | name   | department_id | role |
| --: | ------ | ------------: | ---- |
|   1 | João   |            10 | DEV  |
|   2 | Maria  |            10 | HR   |
|   3 | Carlos |            20 | DEV  |
|   4 | Ana    |            10 | DEV  |
|   5 | Pedro  |            20 | HR   |
### Implementação

```SQL
CREATE INDEX idx_users_department_role
ON users (department_id, role);
```