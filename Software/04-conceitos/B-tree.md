Estrutura padrão utilizada para [[Índices]] em banco de dados.



### B+Tree
Uma especificidade importante: os bancos de dados usam, principalmente a versão `b+tree`

![[Pasted image 20260817101848.png]]

#### B-tree
Os valores da árvore podem estar tanto em um nó, quanto em uma folha
#### B+tree
Os valores da árvore estão apenas nas folhas, e os nós funcionam apenas como placas de direção: "maior que 20, siga pra ca. Menor que 20, para lá"

### Busca pontual
Cada nó guarda centenas de chaves ordenadas — as placas —, e cada uma marca a fronteira entre duas faixas. O banco compara o valor buscado, desce por **uma** faixa e descarta todas as outras de uma vez.

Por isso a árvore é **rasa e larga**: 10 milhões de linhas cabem em ~3 níveis (uma árvore binária precisaria de ~24). Na folha, o índice aponta para a linha na tabela, onde estão as demais colunas.

### Por que essa diferença?
Num range (`BETWEEN 10 AND 40`), a busca desce **uma vez** até o limite inferior e depois anda pelas folhas encadeadas até o superior, sem voltar à raiz — não é uma busca por valor.

Isso só funciona porque os valores estão apenas nas folhas: percorrê-las é percorrer todos os valores, em ordem. Numa B-tree comum, parte dos valores fica nos nós do meio, e o mecanismo teria de subir e descer para não perdê-los.

### Muito bom para
- Buscas por range
	- Perceba como o intervalo entre 10 e 40, por exemplo, fica agrupado à esquerda da árvore na imagem
- Buscas pontuais
- `ORDER BY` na coluna indexada: as folhas ordenadas e encadeadas já entregam em ordem, então o `Sort` some do plano (com hash: `Seq Scan` + `Filter` + `Sort`)

Outras alterantivas são o hash e o Composite

### Contraste com hash
O hash é ótimo para igualdade (`WHERE email = 'x'`) e, em tese, mais direto que percorrer a árvore. Mas ele **destrói a ordem**: a função de hash espalha os valores propositalmente, então valores vizinhos (10, 11, 12) caem em posições sem relação nenhuma entre si.

Sem ordem, o hash não atende:

| Operação | Por quê |
| -------- | ------- |
| `WHERE idade BETWEEN 10 AND 40` | não existe "próximo valor" para percorrer |
| `ORDER BY name` | o índice não entrega os dados ordenados |
| `MIN` / `MAX` | não há primeira nem última folha |
| `LIKE 'abc%'` | prefixo é um range disfarçado (`>= 'abc'` e `< 'abd'`) |

É por isso que o B+tree é o default do Postgres: as folhas ficam ordenadas e encadeadas, então achar o início do range e caminhar pelas folhas resolve todos os casos acima. O hash só compensa quando a coluna é usada **exclusivamente** com `=`.