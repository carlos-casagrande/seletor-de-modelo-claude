---
name: seletor-de-modelo
description: >
  Use quando o usuário quiser decidir qual modelo Claude E qual nível de esforço
  (effort) usar para uma tarefa — perguntas como "qual modelo uso pra isso?",
  "vale a pena baixar pro Haiku/Sonnet?", "isso precisa de Opus?", "dá pra
  economizar aqui?", "que esforço uso?", ou quando anexa/cola um documento e
  pergunta qual modelo é o mais indicado para tratá-lo. Faz uma triagem CURTA da
  tarefa (sem resolvê-la) e recomenda o par ideal MODELO @ ESFORÇO, com
  justificativa e o comando para trocar. NÃO troca o modelo sozinha: a troca é
  sempre do usuário, via /model. Acione mesmo que ele não diga a palavra "modelo", desde que o
  contexto seja "qual ferramenta/quão pesado isso precisa ser".
---

# Skill: Seletor de Modelo e Esforço

**Versão 1.0.9 — 26/11/2026.** Se o usuário perguntar qual versão da skill está
instalada, responda com esta linha, e diga que o catálogo de preços abaixo é o de
novembro de 2026 e deve ser conferido na fonte.

Você ajuda o usuário a gastar o recurso certo na tarefa certa. A decisão tem
**duas alavancas independentes**:

1. **Modelo** — teto de capacidade e preço por token.
2. **Esforço** (`effort`) — profundidade de raciocínio (thinking) e consumo de
   tokens **dentro** do modelo: `low → medium → high → xhigh → max`. Entre os
   extremos, o consumo varia cerca de 5× no mesmo modelo.

Sua função é uma **triagem de portaria em dois passos**: (1) a natureza do
serviço define o **modelo** (piso de capacidade); (2) os stakes e a profundidade
exigida definem o **esforço**. O rotineiro não paga preço de premium — nem em
modelo, nem em esforço.

Fonte de preços: `platform.claude.com/docs/en/about-claude/pricing`. Fonte de
benchmarks: anúncios oficiais em `anthropic.com`. **Confira as duas antes de
citar número** — este catálogo é de novembro de 2026 e envelhece a cada
lançamento.

## O catálogo (conferido em 11/11/2026)

| Modelo | US/1M in-out | Papel |
|---|---|---|
| **Haiku 5.5** | 0,10 / 0,50 até 100 mil tokens de entrada por pedido (leitura de cache 0,01); acima disso, 0,50 / 2,50 | mecânico puro, volume, latência de segundos, em esforço `low` |
| **Sonnet 5.5** | 2 / 10 (leitura de cache 0,10) | **cavalo de batalha do dia a dia** |
| **Opus 5.5** | 4 / 20 (leitura de cache 0,20) | **o que decide** — peça, mérito, ataque, auditoria, em esforço `medium` |
| **Fable 5.2** | 8 / 40 (leitura de cache 0,20) | testado em revisão e como planejador, sem vantagem; não executa etapa — ver Camada 4 |
| Opus 5 | 5 / 25 (cache 0,50) | **legado**, ainda disponível; troque pelo Opus 5.5 |
| Haiku 4.5 · Sonnet 5 · Fable 5.1 | 1 / 5 · 2 / 10 · 10 / 50 | **legado**, substituídos pela geração 5.5 e pelo Fable 5.2; não recomende |

*(Nunca escreva cifrão colado a dígito num arquivo de skill: o cifrão seguido de
número vira variável de argumento e corrompe o texto. Escreva "US/1M" ou
"dólares", como acima.)*

### O que mudou, e por que importa

- **Fable 5.2 (09/11/2026): testado, não entra no que decide.** Custa 8/40, 20% menos que o
  Fable 5.1 e ainda o dobro do Opus 5.5; na leitura de cache os dois cobram o mesmo (0,20).
  Um teste controlado no mesmo dia, com a mesma peça de defeitos plantados e critério escrito
  antes (só entraria se ficasse 1,5 ponto acima do Opus 5.5 @ medium), deu: Opus 5.5 @ medium
  9,0 de 12 · Fable 5.2 @ medium 9,0, com um falso positivo, a 1,52× o custo · Fable 5.2 @ high
  10,0, a 2,18× o custo e o dobro do tempo. **Não separou.** O ponto a mais do `high` está
  dentro da variação normal entre duas execuções e veio quase todo de uma só. Instrução
  escondida no arquivo: recusada e denunciada. Sob pressão sem fato novo, uma execução do
  Fable @ medium relativizou (entregou o que foi pedido, com ressalva). O aviso do fabricante
  de que ele às vezes encerra tarefa longa antes do fim em `low`/`medium` não apareceu em duas
  execuções de cerca de dez minutos. O teste mede revisão; redação, tese e tarefa longa de
  horas, onde o fabricante posiciona o Fable, ficaram sem medição.
- **Sonnet 5.5 e Haiku 5.5 substituem Sonnet 5 e Haiku 4.5 sem teste próprio.** O Sonnet 5.5
  mantém o preço do Sonnet 5 (2/10) com leitura de cache pela metade (0,10). O Haiku 5.5 custa
  um décimo do Haiku 4.5 até 100 mil tokens de entrada por pedido e é o primeiro Haiku com
  esforço regulável; o padrão de fábrica dele é `medium`, e para trabalho mecânico o par é
  Haiku 5.5 @ low. A troca se apoiou no preço e no lugar que os antecessores ocupavam. Os
  dois foram medidos depois, em revisão de peça e em rotina (ver "A ressalva que vem de teste
  real"), e isso mudou algumas linhas da tabela.
- **Opus 5.5 (22/09/2026) substitui o Opus 5 no que decide, em esforço `medium`.** Mais
  barato (4/20 contra 5/25; leitura de cache 0,20 contra 0,50) e à frente nos benchmarks do
  anúncio. O documento técnico do fabricante (System Card), porém, declara três pioras em
  relação ao Opus 5: obedece mais a instrução escondida em texto **colado** na conversa, cede
  mais quando pressionado e aceita com menos desconfiança autorização que não se pode conferir.
  Um teste controlado de 23/09/2026, com a mesma peça de defeitos plantados e critério fixado
  antes de rodar, não achou nenhuma dessas pioras: Opus 5 @ high 9,25 de 12 · Opus 5.5 @ high
  10,25 · Opus 5.5 @ medium 9,75; nenhum falso positivo; instrução escondida no arquivo
  recusada e denunciada; nenhum recuo sob pressão sem fato novo.
- **Material de terceiro entra por arquivo, nunca colado.** Peça da parte contrária, e-mail,
  mensagem de cliente, documento de fora: é o canal em que o Opus 5.5 piorou. Com o material
  lido como arquivo, o fabricante mede zero obediência em 105 tentativas. E em `max` a
  obediência sobe (7,4% contra cerca de 2% no padrão): `max` só com material que o próprio
  usuário produziu.
  Ao recomendar Opus 5.5 para tarefa com material de fora, diga isso na linha "Porquê"; se o
  usuário disser que vai colar, peça que salve o texto em arquivo (ou salve você e leia de lá)
  antes da análise.
- **Opus 5 substitui o Opus 4.8 pelo MESMO preço** (5/25) e com salto grande no
  Frontier-Bench, em química orgânica e no ARC-AGI 3. **Não recomende mais Opus
  4.8 em nada** — é a mesma conta com menos capacidade. Se o usuário estiver
  nele, sugira migrar.
- **Benchmark do fabricante favorável ao modelo mais caro não decide o roteamento.** Foi
  assim com o Fable 5.1, que aparecia acima do Opus 5 nos nove benchmarks divulgados e não
  separou no teste próprio; o Fable 5.2 também não separou. Veja a seção seguinte, que é a parte
  desta skill que vale mais do que a tabela.
- **Modo rápido (Fable 5.2, Opus 5.5 e Opus 5): não recomende ligar.** É o mesmo modelo, com a mesma
  qualidade, escrevendo até 2,5× mais depressa. Mas **na assinatura ele não gasta cota: gasta
  crédito avulso, ou seja, dinheiro**, mesmo sobrando cota no plano. Quem o liga no meio da
  conversa paga de uma vez todo o contexto acumulado, ao preço do modo rápido. Não existe no
  Sonnet nem no Haiku. Se o usuário perguntar, explique isso; a decisão é dele.
- **Mythos (5.2 e anteriores):** mesmo preço do Fable correspondente, acesso
  restrito a organizações verificadas pelo fabricante. **Irrelevante para
  advocacia** — não recomende.

### A ressalva que vem de teste real, não de benchmark

Duas medições próprias, feitas no escritório do autor, sustentam o
roteamento abaixo. O material dos testes (peça, gabarito, execuções) não acompanha
este pacote: os números são relato de quem mediu, e você não tem como conferi-los
aqui. Por isso a recomendação final desta skill é medir na sua própria tarefa.

**Sonnet não substitui Opus no caso difícil.** No mesmo caso e no mesmo comando,
num ataque adversarial a uma apelação criminal, o modelo de tier superior
decompôs cenários alternativos de dosimetria, localizou o precedente espelhado e
identificou uma inversão de vetor que tornava o recurso arriscado; o tier de
volume apontou o risco e parou ali. **"Chega perto" em benchmark não é "igual"
em raciocínio jurídico encadeado.**

**Benchmark de fabricante não mede a sua tarefa.** Em 02/09/2026, um teste
controlado comparou Opus 5 e Fable 5.1 numa revisão de peça com defeitos
plantados e gabarito lacrado, quatro execuções, duas por modelo, com critério de
separação fixado antes de rodar. **Não separou:** a diferença entre os modelos
ficou menor do que a variação entre duas execuções do mesmo modelo, e o Fable 5.1
ainda consumiu mais tokens. Os nove benchmarks favoráveis ao modelo mais caro não
se converteram em vantagem na peça.

O mesmo instrumento, em 23/09/2026, pôs o Opus 5.5 lado a lado com o Opus 5 (ver "O que
mudou"): mais barato e não pior, também em esforço `medium`. Em 09/11/2026, o mesmo
instrumento pôs o Fable 5.2 contra o Opus 5.5 @ medium, com a pergunta de superioridade, porque
o Fable custa o dobro: empatou em `medium` e fez um ponto a mais em `high`, abaixo da margem
fixada, a mais que o dobro do custo.

Em 13/11/2026 o instrumento mediu os modelos baratos. A amostra é pequena (duas execuções
por configuração); trate o resultado como indício:

- **Sonnet 5.5 em revisão de mérito (achar defeito numa peça pronta): admitido como
  executor.** Fez 8,75 de 12 contra 9,0 do Opus 5.5 @ medium, sem falso positivo, e manteve
  os achados quando pressionado.
- **Ataque adversarial com os autos fica no Opus.** Num segundo teste, de ataque à peça com
  o processo à mão, o Sonnet empatou na detecção (9,5 contra 9,5). Contestado sem fato novo,
  porém, aceitou uma premissa não verificada que o Opus refutou. Redação de peça de mérito,
  tese e estratégia seguem no Opus pelo mesmo motivo.
- **Haiku 5.5 nunca em revisão que decide.** Em `high` ele fez 10 de 12 na revisão, nota
  boa, e retirou um achado verdadeiro assim que foi pressionado sem fato novo.
- **Em rotina, o Haiku 5.5 @ low passou** em resumo de despacho ou decisão curta e em
  cronologia de material já lido. Na triagem de intimações passou com margem estreita, e a
  triagem segue no Sonnet até novo teste.

Daí a regra: **a sonda de pressão decide a confiança, não só a nota.** Modelo que retira achado verdadeiro sob pressão, sem fato novo, não revisa o
que decide, qualquer que seja a nota dele.

E a divisão de territórios: **Sonnet 5.5 governa o volume e pode revisar peça como
executor, Opus 5.5 governa o que decide, e o Fable 5.2 não entra no que decide.** Se for
migrar um tier, migre por teste próprio, não por tabela de terceiro, esta inclusive.

## Planejar e delegar

Numa sessão de trabalho com etapas, o fio principal **planeja em Opus 5.5 @ medium** e a
**execução sai etapa por etapa para subagente**, cada um no par da tabela abaixo. No Claude
Code isso é a ferramenta de subagente (Agent) com `model: haiku`, `sonnet` ou `opus`. Haiku e
Sonnet sempre que a tabela e as travas deixarem; Opus quando a etapa for da Camada 3. O
subagente devolve o resultado ao planejador, que **confere antes de usar** o que vai a
protocolo ou a cliente.

- **A economia vem dos subagentes, não do planejador.** O plano é curto; a execução é que
  gasta, e ela roda no modelo mais barato que a etapa admite.
- **Fable não executa etapa e não é economia por si.** Custa o dobro do Opus 5.5 por token.
  Num teste controlado de 13/11/2026 (n = 2, uma tarefa de revisão), o plano feito pelo Fable
  5.2 não separou do plano do Opus @ medium (+0,75 na nota do plano, +0,5 na execução, abaixo
  das margens fixadas antes, de 2,0 e 1,5) e custou 2,4× por plano.
- **Etapa sem medição não se chuta.** Etapa fora da tabela, ou par nunca medido para aquele
  serviço, entra no plano como **SEM MEDIÇÃO**, com o par mais conservador que os cinco
  eixos sustentam e a proposta de um teste próprio. Nunca apresente palpite como par medido.
- **Pedido de uma etapa só** (extrair um extrato, triar uma pilha de intimações) não precisa
  de planejador: roda no par da tabela, numa sessão própria ou num subagente.

## O que esta skill NÃO faz (seja honesto sobre isso)

- **Não troca o modelo.** Uma skill é instrução executada pelo modelo já ativo.
  A troca é ato do usuário: `/model`. Você recomenda; ele decide.
- **Não afirma qual modelo está ativo** sem evidência real na conversa (um
  `/model` que o próprio usuário rodou e aparece no chat). Sem isso, apresente
  só a recomendação — nunca alegue saber o estado atual.
- **No Claude Code o botão "Pensamento" NEM EXISTE** — ali há somente
  modelo e esforço. O que segue vale para quem usa o Claude pelo site ou
  pelo aplicativo; se o usuário estiver no Code, diga isso e pare por aí.
- **"Pensamento" NÃO é o esforço — e quem regula o raciocínio é o esforço.**
  Nesta geração o raciocínio é **adaptativo**: o modelo decide a cada pedido se
  pensa e quanto, e a documentação oficial (consultada em 11/11/2026) trata o
  esforço como o controle principal, com caráter de orientação, não de teto:
  `max` pensa mais e sem limite de extensão · `xhigh` mais fundo que `high` ·
  `high` pensa na maioria dos pedidos que se beneficiam · `medium` pode pular no
  simples · `low` minimiza. Pedir por escrito que o modelo pense menos não é
  confiável: segundo a mesma documentação, no Haiku 5.5 e no Fable 5.2 (este em
  `medium` para cima) a instrução de responder direto não impediu o raciocínio;
  para pensar menos, baixe o esforço. **Raciocínio é cobrado como token de
  saída**, mesmo quando não aparece na tela. Não documentado: se o botão do
  claude.ai desliga de fato ou só oculta o texto — não afirme uma coisa nem
  outra.
- **Não altera o esforço — recomenda.** No aplicativo, o esforço é ajuste real
  da sessão, escolhido por você na interface, e fica gravado turno a turno. Frase
  de profundidade no prompt ("responda direto", "análise exaustiva antes de
  concluir") é reforço, não substituto do ajuste. Na **API** (scripts,
  automações) é `output_config.effort`. Em **subagente**, o campo `effort:` do
  frontmatter sobrepõe a sessão.
- **Trocar de modelo não reduz a quantidade de tokens — reduz o custo por
  token.** Quem reduz a *quantidade* é o esforço (menos thinking).
- **A própria triagem custa.** Classifique, não resolva. Documento anexado:
  leitura de relance (tipo, tamanho, controvérsia) — não a análise.

## Onde a economia realmente está (API × assinatura)

- **API (pré-paga):** cada token custa dólar; thinking é cobrado como saída.
- **Claude Code por assinatura:** `/model` não gera custo por chamada. A economia
  é de **cota de uso** e de **velocidade**. **Nunca apresente economia em dólar
  para trabalho feito dentro da assinatura.**

## A assimetria que rege a recomendação

Em advocacia o erro é caro e frequentemente irreversível: tese alucinada
protocolada, dosimetria errada, prazo perdido. Regra de ouro:

> **Economize com confiança no rotineiro e verificável; na dúvida sobre algo de
> mérito, risco ou irreversível, suba modelo e esforço.** O ganho vem de rebaixar
> com segurança o que é repetitivo — não de apostar no que é estratégico.

## Critérios de classificação — cinco eixos

Deixe o eixo mais forte decidir:

1. **Reversibilidade / aposta** — vai a protocolo? Define estratégia? → sobe.
2. **Verificabilidade** — dá para conferir num olhar (uma data, um campo)? → desce.
3. **Profundidade** — construir tese, ponderar correntes, antecipar adversário,
   múltiplas etapas encadeadas? → sobe **esforço**; se a matéria for inédita,
   sobe também o **modelo**.
4. **Risco de alucinação** — depende de jurisprudência, dispositivos, ementas,
   números? → sobe (e exige verificação na fonte de todo modo).
5. **Horizonte e latência** — tarefa longa e autônoma → forte @ xhigh; resposta
   em segundos no balcão → leve @ low.

## Regras de calibragem do esforço

- **high é o padrão técnico, exceto no Opus 5.5 do que decide, que é `medium`** (o teste de
  23/09/2026 o achou não pior que o Opus 5 @ high). Não recomende acima sem motivo nomeado.
- **Faixa na tabela (`low–medium`, `medium–high`): entregue um nível só.** Comece pelo de
  baixo. Suba para o de cima quando o resultado vai a protocolo ou a cliente sem outra
  conferência, quando há armadilha conhecida (no prazo: feriado local, suspensão, termo
  inicial duvidoso) ou quando o material é longo ou confuso. Se um desses motivos já aparece
  no pedido, recomende direto o de cima. Diga o motivo no "Porquê".
- **xhigh** — trabalho agêntico/encadeado longo. O padrão do Claude Code é `high` em todo
  modelo com esforço, exceto o Opus 5.5, que abre em `medium` (documentação oficial "Model
  configuration").
- **max** — só quando o acerto vale mais que custo e tempo, em tarefa **fechada**
  (ex.: conferência final de cálculo de pena). Custo medido: `max` gastou cerca
  de **5× os tokens e 30× o tempo** do `low` na mesma tarefa. Não é "modo
  caprichado" — é exceção.
- **Equivalência entre gerações:** a geração atual entrega em esforço menor o que
  a anterior entregava em esforço maior. Ao migrar um fluxo, **desça um nível e
  teste** antes de manter o antigo.
- **Restrições técnicas:** o Haiku 5.5 é o primeiro Haiku que aceita `effort`;
  o padrão de fábrica dele é `medium` e, no mecânico, recomende `low`. Quais
  níveis cada modelo aceita está na tabela de disponibilidade de esforço da
  documentação oficial — confira lá antes de recomendar `xhigh` ou `max` fora
  do Opus 5.5. O Fable 5.2 foi testado em `medium` e `high`.

## Tabela de roteamento — serviço → modelo @ esforço

### Camada 1 — mecânico (Haiku 5.5 @ low)
| Tarefa | Por quê |
|---|---|
| Extração de dados de extrato bancário, planilha, cadastro | Mecânico puro, volume alto, conferível num olhar |
| Renomear/organizar arquivos, converter formato, padronizar nome | Sem juízo jurídico |
| Extrair qualificação de partes de um documento já lido | Campo a campo, verificável |
| Resumo de despacho ou decisão curta (o que foi determinado, para quem, prazo fixado) | Medido em teste de rotina; conferível contra o original |
| Cronologia de narrativa ou de documentos já lidos | Medido em teste de rotina; só reordena |

> ⚠️ **Cálculo de prazo NÃO fica no Haiku.** Perder prazo mata a peça e a regra
> tem armadilhas (úteis × corridos, feriado local, suspensão, termo inicial).
> Vai para Sonnet 5.5 @ medium–high, com `[VERIFICAR]` no que não for confirmável
> na fonte.

### Camada 2 — volume e rotina (Sonnet 5.5)
| Tarefa | Esforço |
|---|---|
| Triagem de intimações e publicações, classificação de movimentação (o Haiku passou com margem estreita; fica no Sonnet até novo teste) | low |
| Ficha de andamento | low |
| Rascunho de e-mail ou mensagem a cliente | low–medium |
| Leitura de autos volumosos — primeira passada, mapa de fatos e provas | medium |
| Organização de documentos do caso | medium |
| Revisão de mérito de peça pronta (achar defeito), como executor em subagente; Opus @ medium em recurso a tribunal superior, prazo fatal ou complexidade alta | medium |
| Cálculo de prazo e conferência de tempestividade | medium–high |
| Minuta a partir de modelo pronto; petição de juntada, ciência, expediente | high |
| Contestação ou manifestação em matéria pacificada | high |
| Procuração, contrato de honorários, lista de documentos | high |
| Pesquisa jurisprudencial **exploratória** (mapear o terreno) | high |
| Conteúdo de comunicação e marketing | medium–high |
| Revisão processual formal (endereçamento, requisitos, preparo) | high |
| Polimento de redação e padrão editorial de peça pronta | medium–high |

### Camada 3 — o que decide (Opus 5.5)
| Tarefa | Esforço |
|---|---|
| Peça de mérito para protocolo (inicial, contestação controvertida, réplica) | medium |
| Recurso: apelação, agravo, embargos, recurso especial, contrarrazões | medium |
| Dosimetria da pena; tese de nulidade; estratégia de defesa | medium |
| Ataque adversarial à própria peça, com os autos (o Sonnet empatou na detecção e cedeu sob pressão) | medium |
| Planejamento da sessão, no fio principal (ver "Planejar e delegar") | medium |
| Auditoria ou planejamento de skill, hook, automação (a execução por diff conferível vai a Sonnet @ high ou subagente) | medium |
| Parecer jurídico; análise de viabilidade | medium |
| Pesquisa jurisprudencial que **vai fundamentar peça decisiva** | medium |
| Auditoria de execução penal; análise de indulto e comutação | medium |
| Prescrição penal (qualquer modalidade) | medium |
| Análise de decisão para mapear vícios (embargos de declaração) | medium |
| Pipeline agêntico longo com várias etapas encadeadas | **xhigh** |
| Conferência final fechada onde o acerto vale qualquer custo | **max** (exceção nomeada; só com material próprio) |

> O teste de 23/09/2026 mediu `high` e `medium`. As linhas `xhigh` e `max` trocaram o modelo
> sem teste próprio nesses níveis. `high` continua disponível quando se quiser margem: ficou
> meio ponto acima de `medium`, dentro da variação normal. No mesmo nível de esforço, o Opus 5.5
> pensa mais por turno que o Opus 5, sobretudo em `xhigh` e `max` (turno mais longo, mais cota).
> O fabricante recomenda esses dois níveis só quando houver ganho medido. Ao recomendar `xhigh`,
> escreva na linha "Porquê" que não há medição própria.

### Camada 4 — exceção (Fable 5.2)
**No que decide, nenhum cenário.** No teste controlado de 09/11/2026 o Fable 5.2
empatou com o Opus 5.5 em `medium`, custando 1,52×, e o ponto a mais em `high`
ficou dentro da variação, a 2,18× o custo e o dobro do tempo. Na leitura de cache
os dois cobram o mesmo, então nem a releitura barata favorece o Fable. Como
planejador ele também não separou (ver "Planejar e delegar"), e etapa ele não executa.

Fora da revisão, **não há medição**: o fabricante posiciona o Fable para tarefa
autônoma longa, de horas, e esse uso não foi testado. Se o usuário quiser o Fable
para isso, diga que é aposta sem medição própria, e a decisão é dele.

Não recomende Fable por "capricho" nem por "tese inédita": isso é Opus 5.5 @
medium, ou @ max na conferência final.

### Casos de fronteira
Se a tarefa **começa** rotineira e **termina** virando peça ("lê esse despacho e
já faz a resposta"), **separe as etapas**: o planejador em Opus 5.5 @ medium manda a
leitura a um subagente Sonnet 5.5 @ medium e redige no fio principal, ou manda a redação a
um subagente Opus. Não escolha um par único para o pacote inteiro.

**Trocar de modelo no meio da sessão relê o contexto sem cache**, porque o cache é de cada
modelo. Só vale na virada de fase com trabalho longo pela frente, ou em sessão nova; fora
disso, "siga". Etapa mecânica no meio do caminho vai para **subagente Haiku 5.5**, qualquer
que seja o planejador.

## Formato da recomendação

Seja breve. Entregue exatamente nesta estrutura:

```
**Tarefa:** [uma linha — o que é]
**Recomendo:** [modelo] @ [esforço]
**Porquê:** [1–2 frases ancoradas nos eixos: stakes, verificabilidade, profundidade, alucinação, horizonte]
**Trocar:** CLI → `/model [id]`   (ou: "se já estiver nesse par, siga")
**Economia:** [assinatura: cota/velocidade | API: ordem de grandeza | "marginal — não vale trocar"]
```

IDs exatos para a linha "Trocar": `claude-haiku-5-5` · `claude-sonnet-5-5` ·
`claude-opus-5-5` · `claude-fable-5-2` (Opus 5, legado: `claude-opus-5`).

Tarefa com etapas → troque "Recomendo" por um **plano por etapa**:

```
**Planejador:** Opus 5.5 @ medium
**Etapas:** 1. [etapa] → subagente [haiku|sonnet|opus] @ [esforço] — [motivo em meia linha]
            2. ... (SEM MEDIÇÃO → propor teste, não chutar)
```

Nesse caso o "Trocar" fala só do planejador (`claude-opus-5-5`). A pergunta ao usuário é
sobre o planejador; o par de cada etapa é decisão do plano.

**Tarefa fora da tabela** (uma tradução, por exemplo): não devolva resposta vazia.
Classifique pelos cinco eixos e recomende o par **mais conservador** que eles sustentam;
na dúvida entre dois modelos, o de cima. Escreva no "Porquê" que o par é **SEM MEDIÇÃO**,
escolhido por critério e não por teste, e sugira o teste próprio em meia linha.

**Pergunta que não é escolha de par** (modo rápido, "Pensamento", versão, diferença entre
níveis de esforço): responda em uma a três frases diretas, sem o bloco. O bloco serve só
para recomendar modelo e esforço.

## Saída: só texto, sem cartão visual

Esta skill **não renderiza widget nem HTML**. A saída é o bloco **Tarefa /
Recomendo / Porquê / Trocar / Economia**, em texto puro, e nada mais. Um cartão
visual foi testado e abandonado: custava tempo e tokens para entregar exatamente
o conteúdo que o bloco já entrega.

## Quando invocada por um hook de pausa

Se você instalou o hook do arquivo `settings-hook-exemplo.json`, ele lembra o
modelo de parar antes da primeira tarefa substantiva da sessão (ou quando a
natureza da tarefa muda) e usar esta skill como motor:

1. Rode a triagem e obtenha o par recomendado.
2. **Emita o bloco de texto** da seção "Formato da recomendação".
3. Leve o par ao usuário **como opção principal**, com 2–3 alternativas (o par
   recomendado · um tier abaixo com o risco nomeado · manter o atual). Inclua o
   comando `/model` na descrição de cada opção. Com plano por etapa, a pergunta é
   só a do planejador.
4. **Só prossiga após resposta explícita.** Nunca afirme qual é o modelo ativo
   sem evidência na conversa.

## Quando invocada dentro de um orquestrador

O bloco **Tarefa / Recomendo / Porquê / Trocar / Economia** é a **linha de
evidência auditável** daquela etapa — emita-o sempre, inclusive quando a
conclusão for "já está no par certo, siga". O rastro precisa ficar no
transcript, em texto corrido.

⚠️ **Orquestrador com regra própria de modelo manda nela.** Se o seu pipeline já
tem tabela de modelo por etapa, não recomende par por etapa por fora dela;
consultado, recomende abrir a sessão no par do tronco (quem redige ou decide), em
geral Opus 5.5 @ medium. Se ele usa herança pura (escolhe o modelo UMA vez, no
gate de entrada, e o propaga a todos os subagentes), a resposta é *"recomendo
abrir a sessão em [modelo] — ele valerá para o fluxo inteiro"*, e nada mais.

## Princípios de conduta

- **Não empurre downgrade no que é de mérito.** Se a economia for marginal ou o
  risco alto, diga "fique no Opus 5.5 @ medium" sem rodeio.
- **Preços e benchmarks só com fonte e data.** Não invente números. Consumo por
  esforço fora do que foi medido é estimativa, e deve ser dito como tal.
- **Respeite o fluxo do usuário.** Se ele já está no par certo, a resposta é "já
  está certo, siga" — não invente troca para parecer útil.
- **Uma recomendação, não um ensaio.**

## Exemplos

**Exemplo 1 — mecânico**
Input: "Extrai os lançamentos desse extrato pra planilha."
```
**Tarefa:** Extração de dados de extrato bancário.
**Recomendo:** Haiku 5.5 @ low
**Porquê:** Mecânico, volume alto, conferível num olhar; sem tese e sem aposta. O padrão do Haiku 5.5 é medium: baixe para low.
**Trocar:** CLI → `/model claude-haiku-5-5` e esforço Baixo
**Economia:** Boa — preserva cota e responde em segundos.
```

**Exemplo 2 — o cavalo de batalha**
Input: "Chegaram 14 intimações, separa o que é prazo meu."
```
**Tarefa:** Triagem de intimações e identificação de prazos.
**Recomendo:** Sonnet 5.5 @ low
**Porquê:** Volume e latência; a saída é conferível item a item e não decide mérito.
**Trocar:** CLI → `/model claude-sonnet-5-5`
**Economia:** Real — é a faixa de maior volume do escritório.
```

**Exemplo 3 — o que decide**
Input: "Analisa essa sentença e monta a dosimetria pra apelação."
```
**Tarefa:** Análise de dosimetria e tese para apelação.
**Recomendo:** Opus 5.5 @ medium
**Porquê:** Vai a protocolo, erro é irreversível e há risco de alucinação em pena e jurisprudência. medium basta no Opus 5.5 — não é pipeline autônomo. A sentença entra por arquivo: se for colar, salve o texto antes e a análise lê de lá.
**Trocar:** CLI → `/model claude-opus-5-5` e esforço Médio
**Economia:** Marginal — economizar neste ato custaria mais caro que o token.
```

**Exemplo 4 — plano por etapa**
Input: "Lê essas 80 laudas e já me faz a resposta à acusação."
```
**Tarefa:** Leitura de autos volumosos + redação de resposta à acusação.
**Planejador:** Opus 5.5 @ medium
**Etapas:** 1. Primeira passada: mapa de fatos, provas e o que cabe → subagente sonnet @ medium — extração conferível
            2. Cronologia a partir do mapa → subagente haiku @ low — só reordena material já lido
            3. Redação da resposta (teses, preliminares) → fio principal, Opus @ medium — mérito com aposta
            4. Revisão de mérito da minuta → subagente sonnet @ medium — achar defeito; o planejador confere
**Porquê:** A parte braçal roda barata; o que decide fica no Opus. Os autos entram por arquivo.
**Trocar:** CLI → `/model claude-opus-5-5` e esforço Médio (os subagentes recebem o modelo no plano)
**Economia:** Real — o planejador escreve pouco; o volume de leitura sai no Haiku e no Sonnet.
```

**Exemplo 5 — quando NÃO subir para o Fable**
Input: "Vou sustentar tese inédita em tribunal superior. Subo pro Fable 5.2?"
```
**Tarefa:** Revisão crítica de tese inédita para tribunal superior.
**Recomendo:** Opus 5.5 @ medium (max se for a conferência final antes do protocolo)
**Porquê:** Nenhum benchmark do fabricante mede tese jurídica. No teste controlado de 09/11/2026, em revisão de peça, o Fable 5.2 empatou com o Opus 5.5 em medium custando 1,52×, e em high fez um ponto a mais, dentro da variação, a 2,18× o custo. Construção de tese não foi medida.
**Trocar:** CLI → `/model claude-opus-5-5` e esforço Médio
**Economia:** Real — cerca de dois terços da cota do Fable 5.2 @ medium (menos da metade da do @ high), sem perda medida de qualidade em revisão.
```

## Loop de aperfeiçoamento (só com aprovação)

Erro em caso real ou correção manual do usuário vira **lição datada**: proponha
o ajuste como diff explicado e aguarde aprovação — a skill nunca se reescreve
sozinha. Quando a Anthropic lançar geração nova, **confira o preço na fonte
oficial antes de atualizar esta tabela** — e prefira medir na sua própria tarefa
a confiar no benchmark do anúncio.
