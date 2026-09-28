Testes de software são uma forma organizada de avaliar a qualidade de um programa e garantir que ele funcione como o esperado.

# Objetivos e princípios

Seus principais objetivos e princípios são:
- Encontrar falhas, não provar que o sistema está perfeito
- Verificar e validar se o software segue os requisitos definidos
- Uma aplicação testada não garante que ela está livre de bugs, isso exigiria testar todas as combinações possíveis e isso é impossível

> Teste difícil de escrever é sintoma de acoplamento, não de teste chato. A testabilidade é propriedade do desenho do código. - CODE, Claude

# Níveis de teste

#### Unidade
Um [[teste de unidade]] testa pequenas partes isoladas do código, como funções ou métodos. São baratos de manter, de rápida execução e garantem que a fundação da aplicação é sólida.
#### Integração
O [[teste de integração]] testa se diferentes módulos, serviços ou componentes funcionam bem juntos.
#### Sistema (E2E)
Um [[teste e2e]] avalia o programa completo em um ambiente simulado de uso real
#### Aceitação
O [[teste de aceitação]] é a homologação do sistema. Confirma se o sistema está pronto para o cliente final aprovar e usar.

# Técnicas de caixa
#### Preta
O [[teste caixa preta]] testa o comportamento externo através de entradas e saídas (I/O) sem ver o código fonte
#### Branca
Já no [[teste caixa branca]] testa a estrutura interna, o caminho do código e a lógica de programação

# Testes automatizados vs Manuais
#### Manuais
Os [[testes manuais]] dependem de pessoas para avaliar o objeto do teste. São mais baratos e rápidos que os automatizados. Úteis e adequados em
- [[Testes exploratórios]]
- Avaliação de usabilidade
- Revisão visual
- Cenários em que o comportamento ainda não está bem definido
#### Automatizados
No caso dos [[testes automatizados]] o responsável pelo teste escreve um código que executa diversas ações na aplicação e avalia o retorno desses comandos. Mais caros e de implementação mais lenta que os testes manuais. São mais adequados para:
- Cobertura de regressão
- Fluxos de trabalho repetíveis
- Testes que precisam ser executados com frequência e consistência

# Pirâmide de teste
Demonstra graficamente os tipos de testes, sua distribuíção no plano de testes, seus níveis e velocidade de implementação e complexidade.

![[Pasted image 20260924092657.png]]
### A base
Testes de base são os de unidade. Devem ser maioria no sistema devido as inúmeras pequenas funções de um sistema e da robustez que traz para a base da aplicação, além de serem de rápida implementação e baratos de manter.
### O topo
Os testes do topo da pirâmide (comumente os E2E) são minoria devido a sua complexidade, custo de manutenção e abrangência. Não há como testar o fluxo completo de uma aplicação de 35 formas diferentes, portanto, geralmente poucos testes E2E são o suficiente.

# Test Double
Um test double é qualquer valor fictício utilizado para isolar os testes. Se uma implementação depende de um `user.findById()`, você pode forçar um retorno de um usuário de teste para que essa implementação não fique sujeita aos erros que podem acontecer no banco de dados.

Um dos perigos do test double é o código legado. Um teste que utiliza test doubles e não foi atualizado com a última versão da API que mudou o retorno de um repositório vai continuar retornando verde pra sempre e a aplicação quebra em produção.
### Stub
### Mock
### Fake
### Spy

# Cobertura

> 100% de cobertura não é critério de qualidade. Um teste que chama a função e não afima nada cobre 100% das linhas e verifica zero - CODE, Claude