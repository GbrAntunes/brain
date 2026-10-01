---
name: revisao-espacada
description: Conduz uma sessão diária de estudo (~20 min) sobre as notas em Obsidian. No início pergunta se o usuário quer um tema específico (busca nas notas) ou se pode escolher com base no daily. Abre com um aquecimento de 2–3 perguntas rápidas sobre gaps antigos registrados no daily, depois abre só a nota do tema, faz perguntas baseadas no conteúdo dela, corrige, registra a sessão no daily e joga as lacunas das notas como checklist no inbox. Use sempre que o usuário disser que quer estudar/revisar, "bora estudar", "vamo estudar", "revisão do dia", "o que eu estudo hoje?", ou invocar /estudar.
---

# Rotina de Estudo

Sessão curta e diária (~20 min) sobre as notas em `04-conceitos/`, em duas partes:

1. **Aquecimento (~5 min):** 2–3 perguntas rápidas sobre gaps de sessões antigas, tiradas do daily.
2. **Tema (~15 min):** 3 a 5 perguntas sobre **um** tema, correção, e registro no daily.

O aquecimento existe porque a escolha do tema favorece notas nunca estudadas — sem ele, tema novo
sempre ganha da revisão e o que travou semanas atrás nunca volta. Não vira aula longa nem quiz
aleatório.

Sem sistema de agendamento: a escolha do tema é por julgamento, lendo o `daily.md`. O daily é a
única memória do que já foi estudado — por isso o registro de cada sessão tem uma linha estruturada
(data + tema), pra que a próxima escolha automática não seja um chute.

## Princípios

1. **Contexto sob demanda, não varredura.** O objetivo é custo controlado, não visão de túnel.
   Leia o `daily.md` (barato) pra decidir; abra a nota-âncora do tema. A partir dela, você **pode**
   seguir um wikilink pra uma nota linkada **quando aquilo for necessário** pra formular ou corrigir
   uma pergunta — mas com limite (ver Fase 3). O que é proibido é varrer `04-conceitos/` inteiro ou
   abrir notas preventivamente "por via das dúvidas".
2. **Pergunta e correção saem da nota, não do seu conhecimento.** Você testa e corrige com base no
   que está escrito na nota do usuário. Se discordar ou achar incompleto, sinalize — mas não
   substitua o conteúdo dele pelo seu. Exceção: no aquecimento, a fonte é o próprio daily — o gap
   e a correção que ficaram registrados na sessão de origem.
3. **Uma pergunta por vez.** Espere a resposta antes da próxima.
4. **Escolha do tema é do usuário primeiro.** Sempre ofereça escolher o tema antes de escolher por ele.

## Mapa da vault (não precisa explorar)

```
Software/
  00-inbox/
    inbox.md      <- to-dos rápidos (checklist). Recebe as sugestões de enriquecer nota
  04-conceitos/   <- notas estudáveis (uma por tema)
  07-daily/
    daily.md      <- log narrativo + memória do que já foi estudado (append, um bloco por dia)
```

Divisão: o `daily.md` conta **como foi a sessão**; o `inbox.md` guarda **o que fazer depois**. Uma
lacuna de nota vira linha nos dois — narrada no daily, acionável no inbox.

## Fase 0 — Sincronizar e situar

1. Cheque se existe remote: `git remote`. Se a saída for **vazia**, pule o passo 2 sem comentar —
   vault local é configuração válida, não erro.
2. `git pull` na raiz da vault. Se der conflito ou falhar, **pare** e avise (o daily muda nas duas
   máquinas; conflito é esperado se esqueceu de dar push antes).
3. Data e hora de início: `date "+%Y-%m-%d %H:%M"`. Guarde a hora — ela vai pra linha de duração
   no registro da Fase 4.

## Fase 1 — Escolher o tema

Pergunte ao usuário, de forma direta:

> "Quer estudar algum tema específico hoje, ou prefere que eu escolha com base no que você tem estudado?"

**Se ele der um tema específico:**
- Procure a nota correspondente em `04-conceitos/` (casa por nome de arquivo; se ambíguo, liste os
  candidatos e confirme). Não abra o conteúdo de todas — só resolva o nome.
- Se não existir nota pra esse tema, avise e ofereça: estudar um tema próximo que exista, ou hoje
  *criar* a nota em vez de ser testado.

**Se ele deixar você escolher:**
1. Leia o `daily.md` e extraia os pares (tema, última data estudada) dos blocos diários.
2. Liste os nomes de `04-conceitos/` (só nomes, sem abrir conteúdo).
3. Monte os candidatos:
   - Notas que **nunca** apareceram no daily -> ainda não estudadas, boas candidatas.
   - Entre as já estudadas, as de data **mais antiga**.
4. Escolha uma e **explique em uma linha o porquê** (ex: "Vou de **Teorema CAP** — nunca apareceu no
   daily" ou "faz 8 dias desde a última vez"). Se o usuário não curtir, ofereça a próxima da fila.

## Fase 2 — Aquecimento

2–3 perguntas rápidas sobre gaps de sessões antigas, antes do tema do dia. Sempre acontece, mesmo
quando o usuário escolheu o tema (nesse caso, leia o `daily.md` agora).

**Escolher os gaps:**
1. Do `daily.md`, colete os pontos de "travou"/"não chegou" e os itens de "Aquecimento" registrados
   como ainda abertos.
2. Descarte os que uma sessão posterior registrou como fechados ("fechou o gap de 13/08") e os do
   tema de hoje — esses voltam na sessão principal.
3. Priorize, nesta ordem: gaps que **se repetiram** em mais de uma sessão; gaps de sessões com
   **7 dias ou mais**; os mais antigos. Prefira gaps de temas diferentes entre si.

**Formular a pergunta** — curta, mas de mecanismo, nunca de definição:
- Formato: **cenário concreto + pedido de resposta em 1–2 frases com o porquê.** Ex.: "Read-heavy,
  N=5 réplicas. Qual `R` e `W` você escolhe, e por quê?" — e não "o que é quórum?".
- Pergunta de definição treina recitar a regra, que é justamente o vício que o aquecimento combate.
- Não revele de que sessão ou de qual erro o gap veio antes da resposta.

**Corrigir:** uma pergunta por vez, correção em 1–2 frases com base no que o daily registrou como
certo. Sem aprofundar — se o gap continua aberto, registre e siga. Não abra notas de conceito no
aquecimento.

## Fase 3 — Sessão de perguntas

1. Abra a nota-âncora do tema (`04-conceitos/<Nome>.md`).
   - **Seguir links (opcional, limitado):** se um conceito linkado na âncora for necessário pra fazer
     uma boa pergunta ou corrigir a resposta (ex: PostgreSQL linka PACELC e a pergunta depende disso),
     você pode abrir **essa** nota linkada. Limites: **um salto a partir da âncora** (não siga
     links-de-links, sem recursão), **no máximo ~2 notas linkadas por sessão**, e **avise o usuário**
     quando puxar uma ("puxei tua nota de PACELC pra fundamentar essa"). Não abra links preventivamente.
2. Se a nota estiver rasa demais pra gerar boa pergunta (título só, stub), **não force**: diga que
   está rasa e sugira que hoje o esforço seja *escrever/expandir* a nota, não ser testado.
3. Gere 3 a 5 perguntas cobrindo os pontos centrais: definição, "por quê / quando usar", e ao menos
   uma de aplicação ou contraste (ex: "CAP vs PACELC — o que PACELC acrescenta?").
4. Faça **uma por vez**. Após cada resposta, corrija com base na nota, apontando lacunas de forma
   objetiva. Se a resposta dele estiver certa mas a nota estiver incompleta, guarde pra sugerir depois.

## Fase 4 — Registrar e fechar

1. **Append no `07-daily/daily.md`** (crie o bloco do dia se ainda não existir). Mantenha esta linha
   estruturada — é o que permite a escolha automática na próxima vez:

   ```markdown
   ## <hoje>
   - **Duração:** <início>–<fim> (<N> min)
   - **Aquecimento:** <por gap: [[<Tema>]] (<data de origem>) — <o gap em poucas palavras> → fechado | ainda aberto: <o que faltou>>
   - **Tema:** [[<Nome>]]
   - **Como foi:** <1-2 linhas: o que ficou sólido, onde travou>
   - **Melhorar nota:** <só se a nota estava incompleta; senão omita>
   ```

   **Duração:** o fim é `date +%H:%M` rodado logo antes de escrever o bloco; `N` é o tempo de
   relógio entre início e fim. Se o usuário pausou no meio (avisou, ou parou pra escrever a nota),
   anote junto — `(<N> min, com pausa de ~<M> min pra <motivo>)` —, porque o tempo de relógio sozinho
   mistura estudo com interrupção. As transcrições do Claude Code são apagadas depois de um tempo;
   esta linha é o único registro duradouro de quanto as sessões levam.

2. **Append no `00-inbox/inbox.md`** — uma linha de checklist por lacuna encontrada, só quando houver.
   Cada linha nomeia a nota e diz, em uma frase curta, **o que falta**:

   ```markdown
   - [ ] [[<Nome da nota>]] — <o que adicionar, em uma linha>
   ```

   Regras: no máximo ~3 linhas por sessão (se achou mais, escolha as que mais doem); nada de
   duplicar item que já está no inbox; e a linha descreve a lacuna, não a resposta pronta — o
   trabalho de escrever é do usuário. Conceito que apareceu na sessão e nem tem nota ainda também
   cabe aqui (`- [ ] criar nota [[Composite Index]] — ...`).

3. `git add` **apenas** `07-daily/daily.md` e `00-inbox/inbox.md` e commit (`estudo: <Nome>`).
   Só então `push`, e **apenas se houver remote** (`git remote`) — sem remote, o commit local
   encerra o passo. Se o push falhar, mostre o erro e as mudanças locais; não tente resolver
   conflito sozinho.

   **Falha de permissão (`403`, `Permission denied`) tem uma causa provável e específica:** o vault
   foi clonado do repositório de outra pessoa e o `origin` nunca foi trocado. Não trate como erro
   genérico — diga isso em uma linha, mostre a saída de `git remote -v` e ofereça a correção:

   ```bash
   git remote set-url origin https://github.com/<usuário>/<repo>.git   # ou: git remote remove origin
   ```

   Deixe claro que **a sessão não se perdeu**: o commit local já está feito e sobe no próximo push.
4. Encerre em uma linha (tema estudado). Não puxe outra rodada a menos que o usuário peça.

## O que esta skill NÃO faz

- Não varre a vault. Segue no máximo 1 salto de link a partir da nota-âncora (até ~2 notas), e só
  quando necessário pra pergunta ou correção — nunca links-de-links nem leitura preventiva.
- Não transforma o aquecimento em segunda sessão: sem abrir notas, sem aprofundar, no máximo 3
  perguntas.
- Não inventa conteúdo além da nota pra "ensinar" — aponta a lacuna em vez de preencher.
- Não edita notas de conceito automaticamente — aponta onde a nota está fraca e deixa o to-do no
  `inbox.md` pro usuário escrever.
- Não escolhe o tema sem antes oferecer a escolha ao usuário.
