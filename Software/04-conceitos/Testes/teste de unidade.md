Pensando que o software é uma máquina, composta por módulos, componentes e peças, **o teste de unidade testa as menores partes dessa máquina.**
Em uma analogia com a produção de um carro e os testes industriais e processos de qualidade, é como se, ao receber um lote de parafusos, testássemos se esses parafusos estão homologados e testados contra as especificações técnicas para onde ele será usado.

No mundo de software, **os testes de unidade testam pequenas funções**, como uma função para realizar um cálculo, uma transformação de dados, algo direto e simples.

# Exemplo
Em uma API de pedidos, podemos criar o seguinte teste:

Dado um pedido com:
- Produto A = R$100
- Produto B = R$50
Quando calcular o total (`calculateOrderTotal()`), o resultado deve ser R$150

**Não precisa de banco, HTTP ou serviços externos**