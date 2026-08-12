Teorema proposto por Eric Brewer que afirma que um sistema de dados distribuído pode garantir no máximo duas de três propriedades: consistência, disponibilidade e tolerância a partições. 
Esse tipo de teorema fala principalmente de bases de dados distribuídas, onde um conjunto de máquinas compõem a base de dados total. Então você pode ter um "pedaço" do banco de dados em São Paulo, uma cópia dele em Brasília, e assim por diante.
### O que significa CAP
#### Consistency (C)
Todos os [^1]nós mostram os mesmos dados ao mesmo tempo. Uma leitura sempre retorna a gravação mais recente.

#### Availability (A)
Cada requisição recebe uma resposta sem erros, mesmo que os dados possam estar desatualizados.

#### Partition (P)
O sistema continua funcionando apesar de perdas ou atrasos de mensagens na rede entre os nós. "Partição" aqui se entende como um corte na comunicação entre dois nós, e esse critério avalia a tolerência a esse corte. Vale lembrar que essa característica não é opcional, o sistema ou é CP ou é AP, caso tirássemos o P, estaríamos considerando que a aplicação pararia de funcionar corretamente.

[^1]: Cada uma das máquinas ou indivíduos (como containers docker) que compõem o sistema. Para nosso contexto, podemos imaginar que um nó é uma base de dados em São Paulo e uma cópia dessa base que está em Brasília.

### Exemplos
Caso haja uma partição (corte de conexão entre dois nós) entre o nó de São Paulo e o nó de Brasília, o que você prefere: continuar exibindo dados desatualizados (disponibilidade) ou travar o sistema até a rede voltar para garantir dados idênticos (consistência)?