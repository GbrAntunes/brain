---
name: estudo-guiado
description: Conduz o estudo de um assunto NOVO (ou de uma nota ainda rascunho) em forma de conversa — pergunta o objetivo do estudo, propõe um roteiro de tópicos, explica um conceito por vez e devolve uma pergunta de mecanismo, corrige separando o que está certo do que não está, e no fim condensa o que foi discutido numa nota curta da vault, separando desdobramentos grandes em notas próprias. Use quando o usuário quiser aprender um tema que ainda não domina, "escrever a nota de X", "me ajuda a entender X", "quero estudar um assunto novo", ou invocar /estudo-guiado. NÃO é para revisar notas existentes (isso é a revisao-espacada).
---

# Estudo guiado

Estudo ativo de um assunto novo, por conversa. O produto final é uma nota em `04-conceitos/`,
mas o valor está no caminho: o usuário raciocina, erra, pergunta, e a nota registra as conclusões
**dele**, já corrigidas.

Existe porque, em assuntos novos (principalmente os da moda), a internet é cheia de conteúdo raso
e contraditório. Ler mais um artigo não resolve; discutir o mecanismo, sim.

Diferença para a `revisao-espacada`: aquela **testa** uma nota que já existe; esta **constrói**
a nota a partir do zero (ou de um rascunho).

## Princípios

1. **O objetivo guia tudo.** Roteiro, profundidade, exemplos e o que entra na nota são escolhidos
   pelo objetivo declarado na Fase 1. Sem objetivo, o estudo vira tour enciclopédico.
2. **Um tópico por vez, uma pergunta por vez.** Explique pouco, pergunte, espere a resposta.
3. **Pergunta de mecanismo, não de definição.** "O que muda de verdade em relação ao REST?" ensina;
   "o que é MCP?" só treina a recitação. Dê uma dica de direção quando a pergunta for difícil
   ("pense em *quem* lê o contrato e *quando*").
4. **Exemplos do contexto real do usuário.** Use as ferramentas, o projeto, a sessão atual e as
   regras do próprio CLAUDE.md dele como exemplo ("na nossa conversa, quem é o host?"). Isso pesa
   mais que qualquer analogia genérica.
5. **Fonte primária quando houver divergência.** Em tema novo ou que muda rápido, ancore na spec e
   na documentação oficial. Se não tiver certeza de algo, ou se for recente demais, busque
   (WebSearch/WebFetch) antes de afirmar. Diga quando algo depende de versão ("versões recentes da
   spec permitem..."). Não repita o senso comum da internet sem checar.
6. **A nota sai da conversa, não do seu conhecimento.** Só entra na nota o que foi discutido e
   concluído. O que você sabe e não foi discutido fica de fora ou vira tópico pendente.

## Fase 0 — Situar

1. `git remote`. Se houver remote, rode `git pull`; se der conflito, **pare** e avise. Sem remote,
   siga em frente sem comentar.
2. Hora de início: `date "+%Y-%m-%d %H:%M"`.
3. Procure se já existe nota do tema em `04-conceitos/` (casando pelo nome do arquivo). Se existir,
   leia: ela é o rascunho de partida. Leia também **uma** nota vizinha, de preferência a mais
   desenvolvida da mesma pasta, só para pegar o estilo da vault. Não varra a vault.

## Fase 1 — Objetivo

Antes de propor qualquer roteiro, pergunte, de forma direta e numa mensagem só:

> "Antes de começar: qual é o seu objetivo com esse estudo? Por exemplo: usar no trabalho, entender
> como funciona por dentro, avaliar se vale adotar, implementar algo, se preparar para uma
> entrevista. E o que você já sabe ou já usou disso?"

Se já existir rascunho, a segunda pergunta pode ser dispensada: o rascunho mostra o que ele sabe.

Com a resposta, anote para você:
- **Objetivo**: em uma frase.
- **Critério de pronto**: o que ele precisa saber responder ao final para o objetivo estar
  cumprido. Exemplo: para "configurar MCPs no time com segurança", o critério é saber escolher
  entre stdio e HTTP e saber onde mora a credencial.

## Fase 2 — Roteiro

Proponha 4 a 6 tópicos, em ordem, filtrados pelo objetivo (o que não serve ao objetivo fica de
fora, ou é citado como "fica para depois"). Para cada tópico, uma linha dizendo o que ele cobre.
Marque já os que provavelmente viram nota própria ("esse provavelmente vira nota separada").

Se houver rascunho, **comece por ele**: escolha a afirmação mais imprecisa e transforme-a na
primeira pergunta. Exemplo: o rascunho dizia que o MCP "não tem contrato como o REST", e a
pergunta foi "um servidor MCP também tem contrato; então o que muda de verdade?".

O usuário pode reordenar, cortar ou acrescentar tópicos.

## Fase 3 — Conversa, tópico a tópico

Ciclo de cada tópico:

1. **Explique pouco**: um parágrafo ou uma tabela curta, com um exemplo concreto. Código ou JSON
   só se o formato for o próprio ponto.
2. **Faça uma pergunta** de mecanismo, aplicação ou contraste, com dica quando for difícil.
3. **Corrija a resposta**:
   - Diga o que está certo, sem rodeio ("Isso, 50").
   - Se houver ideia certa misturada com errada, **separe as duas explicitamente** ("tem uma ideia
     certa aí, mas misturada com outra"). Depois explique o porquê do ajuste.
   - Torne concreto o "por quê" que ele deixou vago. É isso que vai para a nota.
   - "Não sei" é resposta válida: reconstrua a partir do que ele *disse* saber (no caso do MCP,
     "não sei o que vem na response" já era a pista).
4. **Responda às dúvidas dele** diretamente, antes de seguir. Dúvida do usuário vale mais que o
   roteiro.
5. **Faça a ponte para o próximo tópico**, de preferência usando algo que acabou de surgir.

Quando um tópico se mostrar grande demais (vários subconceitos, cada um com seu próprio "por
quê"), **não o espreme na nota principal**. Ofereça:

1. fechar agora, com um link `[[<Tema> - <Subtema>]]` para uma nota futura; ou
2. seguir conversando e escrever as duas notas no final.

Ao chegar perto do critério de pronto, ou quando o usuário pedir para encerrar, siga para a Fase 4.

## Fase 4 — Escrever a nota

Caminho: `04-conceitos/<Categoria>/<Tema>.md`. Use a pasta da categoria que já existe; se for uma
categoria nova, avise que criou. Se havia rascunho, a nota o substitui.

Estilo da vault (siga a nota vizinha lida na Fase 0):
- `### Conceito` abre a nota, com uma ou duas frases.
- Uma seção `###` por tópico discutido, com o **título dizendo a conclusão** quando der ("Onde
  está a 'interface universal'", e não "Interface").
- Tabela quando a conversa comparou alternativas; negrito na frase que carrega a ideia.
- As correções entram como a versão certa, às vezes com o contraste explícito ("a divisão **não**
  é leitura vs escrita: ...").
- Os exemplos que funcionaram na conversa entram na nota (o 5×10 = 50 vs 5+10 = 15).
- Termina com `### Conceitos relacionados` e wikilinks: notas existentes que se relacionam e as
  notas pendentes que ficaram combinadas.

**Curta.** Como referência de tamanho, use a maior nota de conceito da vault. Se passou disso, uma
seção é candidata a nota própria: diga qual.

**Antes de gravar, apresente a estrutura para aprovação**: os títulos das seções, com uma linha
sobre o que cada uma diz, e as notas pendentes que vão como link. Só grave depois do ok. O usuário
quer decidir o recorte da nota antes de ela existir.

## Fase 5 — Registrar e fechar

1. **Append no `07-daily/daily.md`** (crie o bloco do dia se não existir):

   ```markdown
   ## <hoje>
   - **Duração:** <início>–<fim> (<N> min)
   - **Estudo guiado:** [[<Tema>]] — objetivo: <uma frase>
   - **Como foi:** <1-2 linhas: o que ficou sólido, onde travou>
   ```

   Use `Estudo guiado:` e não `Tema:`. A `revisao-espacada` usa `Tema:` para saber o que já foi
   *testado*, e uma nota recém-escrita ainda não foi: ela deve continuar como candidata a revisão.

2. **Append no `00-inbox/inbox.md`**, uma linha por nota pendente combinada na conversa:

   ```markdown
   - [ ] criar nota [[<Tema> - <Subtema>]] — <o que discutir, em uma linha>
   ```

3. `git add` apenas a nota (ou notas) escrita(s), o `daily.md` e o `inbox.md`. Faça o commit
   `estudo: <Tema> (nota nova)` e o push, só se houver remote. Se o push falhar, mostre o erro;
   não resolva conflito sozinho.

4. Feche com:
   - resumo da nota em tópicos (uma linha por seção);
   - **o objetivo foi cumprido?** Compare com o critério de pronto da Fase 1 e diga o que faltou,
     se faltou algo;
   - pontos de atenção: nota maior que o normal, links pendentes.

## O que esta skill NÃO faz

- Não começa o conteúdo antes de saber o objetivo.
- Não despeja o assunto inteiro de uma vez: um tópico por vez, sempre terminando em pergunta.
- Não coloca na nota o que não foi discutido.
- Não afirma como fato algo recente ou controverso sem checar a fonte primária.
- Não varre a vault: lê o rascunho, uma nota vizinha para o estilo e, se precisar, uma nota
  diretamente relacionada.
