Testes de software são uma forma organizada de avaliar a qualidade de um programa e garantir que ele funcione como o esperado.

# Objetivos e princípios

Seus principais objetivos e princípios são:
- Encontrar falhas, não provar que o sistema está perfeito
- Verificar e validar se o software segue os requisitos definidos
- Uma aplicação testada não garante que ela está livre de bugs, isso exigiria testar todas as combinações possíveis e isso é impossível

# Níveis de teste

#### Unidade
Um [[teste de unidade]] testa pequenas partes isoladas do código, como funções ou métodos
#### Integração
O [[teste de integração]] testa se diferentes módulos ou componentes funcionam bem juntos
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
Os [[testes manuais]] dependem de pessoas para avaliar o objeto do teste. Úteis e adequados em
- [[Testes exploratórios]]
- Avaliação de usabilidade
- Revisão visual
- Cenários em que o comportamento ainda não está bem definido
#### Automatizados
No caso dos [[testes automatizados]] o responsável pelo teste escreve um código que executa diversas ações na aplicação e avalia o retorno desses comandos. São mais adequados para:
- Cobertura de regressão
- Fluxos de trabalho repetíveis
- Testes que precisam ser executados com frequência e consistência

# Pirâmide de teste
# Cobertura