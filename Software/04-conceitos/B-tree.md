Estrutura padrão utilizada para [[Índices]] em banco de dados.

![[Pasted image 20260817101848.png]]

### B+Tree
Uma especificidade importante: os bancos de dados usam, principalmente a versão `b+tree`

#### B-tree
Os valores da árvore podem estar tanto em um nó, quanto em uma folha
#### B+tree
Os valores da árvore estão apenas nas folhas, e os nós funcionam apenas como placas de direção: "maior que 20, siga pra ca. Menor que 20, para lá"

### Muito bom para
- Buscas por range
	- Perceba como o intervalo entre 10 e 40, por exemplo, fica agrupado à esquerda da árvore na imagem
- Buscas pontuais

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