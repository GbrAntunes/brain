---
name: fechar-lacunas
description: Fecha itens abertos do inbox (lacunas de notas apontadas nas revisões) em forma de conversa — escolhe uma nota com itens pendentes, esconde o texto dos itens, coloca o usuário em situações capciosas que levam ao problema que cada lacuna responde, guia com pista graduada até ele chegar à conclusão, e só então escreve a conclusão DELE na nota, com aprovação antes de salvar. Use quando o usuário quiser "matar o inbox", "fechar lacunas", "melhorar as notas", "trabalhar os itens do inbox", ou invocar /fechar-lacunas. NÃO é para testar uma nota (revisao-espacada) nem para assunto novo (estudo-guiado).
---

# Fechar lacunas

Sessão de ~20–30 min que transforma itens do `00-inbox/inbox.md` em texto na nota. A nota só muda
depois que o usuário **chega sozinho** à conclusão que o item pede, e a redação parte das palavras
dele.

Existe porque as revisões encontram lacunas mais rápido do que as notas são reescritas, e a revisão
seguinte testa o mesmo texto incompleto. Escrever a nota pelo usuário resolveria o inbox sem que ele
aprendesse nada; escrever a frio, sozinho, não tem gatilho e não acontece.

Diferença para as outras skills: a `revisao-espacada` **testa** a nota como ela está; o
`estudo-guiado` **constrói** uma nota nova; esta **completa** uma nota existente, lacuna por lacuna.

## Princípios

1. **O item fica escondido.** Muitos itens do inbox já trazem a resposta no texto ("custo do
   `fsync` = latência por commit"). O usuário não lê o item antes nem durante: você anuncia só a
   nota e quantas lacunas vai atacar. Não leia o item em voz alta, não parafraseie a conclusão.
2. **Armadilha, não pergunta de definição.** Cada lacuna vira um cenário concreto em que a resposta
   intuitiva — ou o que a nota diz hoje — leva ao erro. "O banco ficou 3 dias no ar e caiu. Por que o
   recovery não reaplica 3 dias de WAL?" leva ao checkpoint; "o que é checkpoint?" só treina recitar.
3. **A conclusão é dele.** Você conduz, corrige e dá pista; não entrega. A nota recebe o que ele
   concluiu, redigido perto das palavras dele.
4. **Nada é salvo sem aprovação.** Mostre o trecho antes de editar a nota.
5. **Uma pergunta por vez.** Espere a resposta antes da próxima.
6. **Fonte:** a armadilha e a correção partem do item do inbox, da nota e do que o `daily.md`
   registrou sobre aquela lacuna. Conhecimento seu entra para montar o cenário e corrigir, mas se
   for além do que o item pede, sinalize ("isso já não está no item, é complemento").

## Mapa da vault (não precisa explorar)

```
Software/
  00-inbox/inbox.md      <- itens "- [ ] [[Nota]] — lacuna"; "- [x]" = feito
  04-conceitos/          <- notas (com subpastas por área)
  07-daily/daily.md      <- log das sessões (arquivo grande: busque com grep, não leia inteiro)
```

## Fase 0 — Situar

1. `git remote`. Se houver remote, `git pull`; se der conflito ou falhar, **pare** e avise. Sem
   remote, siga sem comentar.
2. Hora de início: `date "+%Y-%m-%d %H:%M"`.

## Fase 1 — Escolher a nota

1. Liste os itens abertos do inbox (`grep '^- \[ \]'`) e agrupe por nota (`[[Nome]]` logo após o
   checkbox). Itens `criar nota [[X]]` **não entram** — nota nova é `estudo-guiado`.
2. Pergunte se o usuário quer uma nota específica. Se ele deixar você escolher, priorize:
   - notas cujos gaps estão **abertos no aquecimento** da `revisao-espacada` (grep no daily por
     "ainda aberto" com o nome da nota) — a próxima revisão vai testar o texto novo;
   - notas com **mais itens abertos** (fechar o pacote reescreve a nota uma vez, de forma coerente);
   - itens **mais antigos**.
3. Anuncie em uma linha: a nota, quantas lacunas, e o porquê da escolha — **sem** dizer quais são
   ("Hoje é o [[WAL]]: 3 lacunas, e o `fsync` segue aberto no aquecimento").
4. Selecione **até 3 itens** dessa nota. Se houver mais, escolha os que mais doem e diga que os
   outros ficam para outra sessão.

## Fase 2 — Preparar (silencioso)

1. Abra a nota-âncora. Para cada item escolhido, localize onde a lacuna mora na nota (a seção que
   está incompleta ou errada).
2. `grep` no daily pelo nome da nota para ver como a lacuna apareceu: o que ele respondeu, onde
   travou. A armadilha mira **esse** erro, não um genérico.
3. Para cada item, defina para você:
   - a **conclusão esperada**, em uma frase;
   - o **cenário capcioso** de abertura;
   - os **3 degraus de pista**, já pensados (ver Fase 3).
4. Ordene os itens do mais fundamental para o que depende dele (checkpoint antes do custo do
   `fsync`, se um usa o outro).

Não mostre nada disso ao usuário.

## Fase 3 — Conversa, lacuna por lacuna

Para cada item:

1. **Abra com o cenário capcioso.** Formato: situação concreta + pergunta de mecanismo, pedindo
   resposta em poucas frases com o porquê. Sem citar o termo que é a resposta.
2. **Corrija separando** o que está certo do que não está, em poucas linhas. Se ele chegou à
   conclusão, vá para o passo 4.
3. **Se não chegou, desça um degrau de pista** e faça uma nova pergunta. A pista aponta pra onde
   olhar, nunca entrega:
   1. **Recortar o cenário** para que a peça faltante seja o centro, não um detalhe entre vários.
   2. **Nomear a categoria** do que se pede, sem dizer o item ("que tipo de registro o banco
      precisaria guardar…").
   3. **Contrastar** duas situações que só diferem na peça faltante.

   **Teto:** se depois do degrau 3 a conclusão não sair, explique em poucas linhas, **não** escreva
   na nota e **não** marque o item. Ele volta numa próxima sessão. Registre como "não fechou" na
   Fase 5. Sem teto, a sessão vira interrogatório.
4. **Peça a formulação dele:** "Como você colocaria isso na nota, em 2–3 frases?" Corrija o que
   estiver impreciso na formulação (termo errado, direção invertida, falta do número), sem
   reescrever o estilo.
5. Guarde a formulação aprovada e siga para o próximo item. **Não edite a nota ainda.**

Vícios conhecidos do usuário para vigiar na correção (ver avaliação de 25/11 no daily): justificar
pelo rótulo ou por "intenção" de um agente em vez do mecanismo; acertar o critério e inverter a
aplicação; parar antes do número final. Se aparecer, aponte pelo nome.

## Fase 4 — Escrever na nota

1. Para cada item fechado, redija o trecho **a partir da formulação dele**: mesmo raciocínio, mesmo
   vocabulário quando correto, encaixado na estrutura existente (a seção onde a lacuna mora; nova
   seção só se não houver lugar). Siga o estilo da nota: curta, tabela quando houver contraste,
   wikilinks para notas que existem.
2. Se a lacuna era uma frase **errada** na nota (não só ausente), substitua — não deixe as duas
   versões convivendo.
3. **Mostre todos os trechos juntos** ao usuário, indicando onde cada um entra, e peça aprovação.
   Aplique os ajustes que ele pedir.
4. Só então edite a nota com `Edit` pontual por trecho — nunca reescreva o arquivo inteiro.

## Fase 5 — Registrar e fechar

1. **Inbox:** troque `- [ ]` por `- [x]` **só** nos itens que fecharam e foram escritos. Itens que
   bateram no teto ficam abertos e intactos.
2. **Daily:** rode `date +%H:%M` e faça append no `07-daily/daily.md` (crie o bloco do dia se não
   existir; se já existir, acrescente as linhas no fim dele):

   ```markdown
   - **Duração:** <início>–<fim> (<N> min)
   - **Inbox:** [[<Nota>]] — <k> de <n> lacunas fechadas
   - **Como foi:** <por item: a lacuna em poucas palavras → fechou sozinho | fechou com pista
     (degrau N) | não fechou: <o que faltou>. 1-2 linhas no total sobre onde travou.>
   ```

   Se o usuário pausou no meio, anote junto da duração, como na `revisao-espacada`.
3. `git add` **apenas** a nota editada, `00-inbox/inbox.md` e `07-daily/daily.md`. Commit
   `lacunas: <Nota>`. `push` só se houver remote; se falhar, mostre o erro e não tente resolver
   conflito sozinho (falha `403`/`Permission denied`: provável `origin` de outra pessoa — mostre
   `git remote -v` e ofereça `git remote set-url`).
4. Encerre em uma linha (nota, quantas lacunas fecharam). Não puxe outra nota a menos que o usuário
   peça.

## O que esta skill NÃO faz

- Não mostra o texto dos itens do inbox ao usuário antes de ele chegar à conclusão.
- Não escreve na nota o que o usuário não concluiu, nem salva sem aprovação.
- Não marca como feito item que bateu no teto de pistas.
- Não cria nota nova (`estudo-guiado`) nem testa a nota inteira (`revisao-espacada`).
- Não ataca mais de uma nota nem mais de ~3 itens por sessão.
