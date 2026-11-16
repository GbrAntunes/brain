### Conceito
Os dados de homologação são **inventados e reconstruíveis por comando**: um seed versionado que permite mexer à vontade e depois voltar a um estado conhecido, sem nunca trazer dado real de prod.

### O estado de um ambiente é tudo onde o sistema escreve
Resetar só o banco depois de um pentest deixa sujo o resto: os 500 usuários continuam no **Clerk**, e os arquivos maliciosos continuam nos **buckets**. No dia seguinte:
- **inconsistência entre fontes**: o Clerk aceita o login de um usuário que o banco não conhece (erro ou registro órfão), e o seed cria usuários no banco apontando para IDs do Clerk que não existem mais;
- **arquivo malicioso ainda servido**, se foi parar no bucket público.

O reset precisa de um **inventário de onde o sistema escreve** (banco, Clerk, buckets, filas, caches), e o seed cria os usuários de teste **no Clerk também**, via API, para as duas pontas nascerem consistentes.

**Isolamento é pré-requisito do reset**: só dá pra limpar o Clerk sem medo porque ele é uma instância exclusiva de homologação. Com dev e prod compartilhando serviços (o caso do portal em [[Paridade entre ambientes]]), o "reset completo" apagaria os usuários reais.

### Seed torna o reset um comando
Sem seed, resetar é restaurar um backup de sei lá quando ou recriar dados na mão. Com seed: **apaga → roda as migrations → roda o seed**. Como as migrations são as mesmas de prod, cada reset também testa se elas rodam do zero.

### De onde vêm os dados

| Fonte | Vantagem | Problema |
|---|---|---|
| **Seed sintético** | seguro, reproduzível, versionado com o código | só tem os casos que alguém escreveu; volume pequeno |
| **Cópia anonimizada de prod** | volume e formatos reais | anonimizar é difícil (texto livre, combinações que reidentificam), e a cópia em si toca dado real |
| **Cópia crua de prod** | — | **nunca**: é a falha de isolamento com outro nome, e com LGPD na conta |

### Bug que depende de dado vira linha do seed
"Só acontece com os dados do cliente X" não justifica puxar o dado dele. O que causa o bug é uma **característica** do dado, não a identidade do cliente:
1. investigar o que é diferente (acento, volume, campo nulo de import legado, e-mail com `+`, e até **inconsistência entre plataformas**, como e-mail no banco e não no Clerk);
2. recriar esse formato com dado inventado;
3. se reproduz, o problema é do dado; se não, falta achar a característica ou é diferença de ambiente.

O próprio report já prova que **o seed está incompleto**. O caso entra no seed de vez, como um teste de regressão. Com o tempo, o seed absorve as esquisitices reais e a vantagem da cópia de prod diminui. Isso inclui estados *estranhos*, não só os bonitos.

### Conceitos relacionados
[[Paridade entre ambientes]], [[Tipos comuns de vulnerabilidade]], [[Infraestrutura como código]]
