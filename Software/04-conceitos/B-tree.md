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