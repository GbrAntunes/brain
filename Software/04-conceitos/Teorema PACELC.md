Extensão do [[Teorema CAP]] para [[Sistemas distribuídos]]. O CAP foca em como um sistema de dados distribuídos responde em casos de falhas como um problema de conexão, interferências, etc. O PACELC analisa também o caminho feliz: caso tudo esteja bem, como a base de dados vai se comportar? Qual sua prioridade?

Então em caso de partição entre os nós, o PACELC te dá a mesma resposta que o CAP daria. Caso não haja partição, o PACELC lhe informa como a base de dados opera.

Para além do CAP já discutido, o PACELC adiciona dois novos integrantes:
### Else (E)
O sistema opera sem falhas de rede
#### Latency (L)
O sistema prioriza respostas rápidas

### Como ler as siglas

![[Pasted image 20260810171456.png]]

Um sistema sendo representado pelo PACELC será sempre a combinação entre duas siglas. Sempre teremos `P`, seguido por algumas das capacidades do CAP, depois um `/E` indicando que a próxima letra indica o caminho feliz.
Uma informação importante que a sigla do E nos dá é a configuração do sistema: caso usemos EC, temos uma replicação de dados síncrona; caso use EL, temos uma configuração assíncrona.
### Exemplos práticos
- PA/EL - Se houver partição, prioriza availability. Caso não haja partição, prioriza latência. Um exemplo é o Amazon DynamoDB, tolerante quando tem algum problema de partição e rápido quando está tudo certo.
- PC/EC - Se houver partição ou não, prioriza consistência. Sistemas bancários são bons exemplos, como o CockroachDB e a maioria dos bancos tradicionais como o [[Software/04-conceitos/Postgresql|Postgresql]].
