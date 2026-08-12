Estrutura padrão utilizada para [[Índices]] em banco de dados.

![[Pasted image 20260812094912.png]]

### Muito bom para
- Buscas por range
	- Perceba como o intervalo entre 3 e 19 (7, 17 e 19), por exemplo, fica agrupado à esquerda da árvore na imagem
- Buscas pontuais
	- Se quero o valor 27, preciso de apenas três nós para encontrá-lo, ao invés de percorrer sequencialmente todos os valores até chegar no 27

Outras alterantivas são o hash e o Composite