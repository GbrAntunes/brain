Pensando que o software é uma máquina, composta por módulos, componentes e peças, **o teste de unidade testa as menores partes dessa máquina.**
Em uma analogia com a produção de um carro e os testes industriais e processos de qualidade, é como se, ao receber um lote de parafusos, testássemos se esses parafusos estão homologados e testados contra as especificações técnicas para onde ele será usado.

No mundo de software, **os testes de unidade testam pequenas funções**, como uma função para realizar um cálculo, uma transformação de dados, algo direto e simples.

# Exemplo
Em uma API de pedidos, podemos criar o seguinte teste:

Dado um pedido com:
- Produto A = R$100
- Produto B = R$50
Quando calcular o total (`calculateOrderTotal()`), o resultado deve ser R$150

**Não depende de banco, HTTP ou serviços externos**

# Vantagens
É extramemente simples, verificar se a função calcularDezPorcento() faz `f(x)=x*0.1` e testar se o input for 10, o output será 1.
É um tipo de teste extremamente barato, simples de manter e que geralmente é o que é feito em maior número na aplicação.
A resposta desse teste também é mais precisa. É como se déssemos um zoom na aplicação para investigar no detalhe. Caso um teste unitário falhe, você sabe exatamente quais linhas olhar.

# O que não testa
Contrato entre módulos/serviços. Em uma aplicação financeira você pode ter duas funções de cálculo monetário, a função A faz um output em centavos e a função B espera um input em reais: ambas funcionam, mas não funcionam juntas. Os testes unitários estão verdes mas a aplicação apresenta bug em produção.