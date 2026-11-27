# Seletor de Modelo e Esforço — instalação

**Versão 1.0.9 — 26/11/2026.** Catálogo de novembro de 2026.

Pacote com uma *skill* para Claude Code que ajuda a decidir **qual modelo Claude
usar** e **com quanto esforço**, tarefa por tarefa, com foco em trabalho
jurídico. Ela recomenda; quem troca é você.

Confira os preços na fonte antes de confiar em número — ver "Validade", ao final.

## Qual versão eu tenho instalada?

Pergunte ao Claude, numa sessão nova:

> Qual a versão da skill seletor-de-modelo?

Ela responde com a linha de versão que carrega no próprio corpo. Se ele não
souber dizer, ou a skill não carregou, ou é uma versão anterior a esta — as
primeiras não traziam número.

Também dá para ler direto no arquivo instalado: a versão é a primeira linha
depois do título, em `~/.claude/skills/seletor-de-modelo/SKILL.md`.

**O que muda entre versões:** o catálogo de preços e o roteamento por camada.
Uma versão velha não quebra nada — ela recomenda com base num catálogo que pode
ter mudado, e é exatamente por isso que o número importa.

## O que vem na caixa

```
LEIA-ME.md                          este arquivo
LICENSE.md                          a licença (CC BY 4.0)
settings-hook-exemplo.json          hook opcional de pausa (leia antes de mesclar)
skills/seletor-de-modelo/SKILL.md   a skill
```

## Instalação (2 minutos)

**Passo 1 — copie a skill.**

Coloque a pasta `seletor-de-modelo` dentro do diretório de skills do Claude Code:

- Windows: `C:\Users\SEU-USUARIO\.claude\skills\`
- macOS e Linux: `~/.claude/skills/`

O resultado deve ser `~/.claude/skills/seletor-de-modelo/SKILL.md`.

Se a pasta `skills` não existir, crie. Para valer só num projeto, use
`.claude/skills/` dentro da pasta do projeto.

**Passo 2 — reinicie o Claude Code.** Skills são carregadas na abertura da
sessão; editar ou acrescentar skill com a sessão aberta não tem efeito.

**Passo 3 — teste.** Numa sessão nova, pergunte:

> Qual modelo eu uso para triar 14 intimações e separar o que é prazo meu?

A resposta certa é um bloco curto, em texto puro, mais ou menos assim:

```
**Tarefa:** Triagem de intimações e identificação de prazos.
**Recomendo:** Sonnet 5.5 @ low
**Porquê:** Volume e latência; a saída é conferível item a item e não decide mérito.
**Trocar:** CLI → /model claude-sonnet-5-5
**Economia:** Real — é a faixa de maior volume do escritório.
```

Se vier um ensaio de três parágrafos, ou um cartão colorido, a skill não
carregou — confira o caminho e reinicie.

## Passo 4 (opcional) — o hook de pausa

Sem hook, a skill responde quando você pergunta. Com hook, o modelo é lembrado
de fazer a triagem **antes de começar** a primeira tarefa da sessão.

⚠️ **Não copie `settings-hook-exemplo.json` por cima do seu
`~/.claude/settings.json`.** Se você já tem hooks, copiar por cima apaga todos.
Abra os dois arquivos e **mescle** apenas o bloco `UserPromptSubmit`. As chaves
que começam com `_` são comentários e devem ser removidas: JSON não tem sintaxe
de comentário.

Faça uma cópia do seu `settings.json` antes de mexer.

## Como usar no dia a dia

Pergunte em linguagem normal — "isso precisa de Opus?", "dá pra economizar
aqui?", "que esforço uso nisso?" — ou cole um documento e pergunte qual modelo é
o mais indicado. A skill classifica a tarefa em quatro camadas:

| Camada | Modelo | O que é |
|---|---|---|
| 1 | Haiku 5.5 (esforço baixo) | mecânico e conferível num olhar, inclusive resumo de despacho curto e cronologia do que já foi lido |
| 2 | Sonnet 5.5 | o volume do dia: ler, resumir, minutar, triar; e revisar peça pronta à procura de defeito |
| 3 | Opus 5.5 (esforço médio) | o que decide: peça, recurso, dosimetria, parecer, ataque à própria tese com os autos |
| 4 | Fable 5.2 | testado em revisão de peça e sem vantagem no que decide; fora da revisão, sem medição |

E recomenda também o **esforço** (`low → medium → high → xhigh → max`), que é a
segunda alavanca: trocar de modelo muda o preço por unidade de trabalho; o
esforço muda a quantidade de trabalho.

**Cálculo de prazo não fica na Camada 1.** Parece mecânico e não é.

## Planejar e delegar (novo na 1.0.8)

Pense num escritório com um sócio e uma equipe. O sócio lê o pedido, divide o
trabalho em etapas e entrega cada uma a quem faz bem e mais barato: a leitura dos
autos vai para o assistente, a peça fica com ele mesmo. No fim, confere o que
recebeu antes de assinar.

No Claude Code o desenho é o mesmo. A conversa principal faz o papel do sócio e
roda no **Opus 5.5 em esforço médio**: ela planeja e confere. Cada etapa sai para
um **subagente**, uma espécie de ajudante que o Claude abre só para aquela tarefa,
já no modelo indicado pela tabela: Haiku para o mecânico, Sonnet para o volume e
para revisar peça, Opus só quando a etapa decide. O ajudante devolve o resultado
e a conversa principal confere antes de usar.

A economia vem dos ajudantes. O plano é curto; quem gasta é a execução, e ela
passa a rodar no modelo mais barato que cada etapa admite.

O Fable 5.2 não entra nesse desenho: custa o dobro do Opus por token e, num teste
pequeno, planejou no mesmo nível dele gastando 2,4 vezes mais. E etapa que ninguém mediu não ganha modelo no chute: a
skill escreve "SEM MEDIÇÃO" no plano e sugere testar antes.

Para pedir, basta algo como: *"planeje isso em etapas e mande cada etapa para um
subagente no modelo da tabela"*. Pedido de uma etapa só, como extrair um extrato,
dispensa planejador e roda direto no modelo da tabela.

## E o botão "Pensamento"?

Antes do resto: **se você usa o Claude Code, esse botão não existe** — ali há somente
modelo e esforço, e esta seção não se aplica a você. O que segue vale para o Claude do
site e do aplicativo.

Lá a resposta economiza discussão: **não é a mesma coisa que o esforço**, e quem
regula o raciocínio é o esforço.

Nos modelos atuais, o próprio modelo decide a cada pedido se pensa e quanto, e a
documentação oficial da Anthropic trata o esforço como o controle principal. Pedir
por escrito que ele pense menos não é confiável: segundo a mesma documentação, no
Haiku 5.5 e no Fable 5.2 a instrução de responder direto não impediu o raciocínio.
Para pensar menos, baixe o esforço.

Vale saber que o raciocínio é cobrado como texto produzido, mesmo sem aparecer na
tela. E uma ressalva honesta: a documentação não esclarece se o botão desliga o
raciocínio ou apenas esconde o texto dele.

## O Opus 5.5 e a regra do arquivo

O Opus 5.5 (22/09/2026) custa menos que o Opus 5 e aparece à frente dele nos testes do
fabricante, mas o documento técnico que acompanha o lançamento declara três pioras: obedece
mais a instrução escondida em texto **colado** na conversa, cede mais quando pressionado e
aceita com menos desconfiança autorização que não se pode conferir. Num teste controlado de
23/09/2026, com peça de defeitos plantados, ele não foi pior que o Opus 5 nem em esforço alto
nem em médio, e nenhuma das três pioras apareceu. Por isso a skill recomenda **Opus 5.5 em
esforço médio** para o que decide.

Uma regra vale com qualquer modelo: **material de terceiro entra por arquivo, nunca colado.**
Isso inclui peça da parte contrária, mensagem de cliente e documento de fora: é material que
outra pessoa controla, e colá-lo é usar o canal em que o Opus 5.5 piorou.

## E o "modo rápido"?

É o mesmo modelo, escrevendo mais depressa. Na assinatura, porém, **ele não gasta cota: gasta
crédito avulso, ou seja, dinheiro**, mesmo sobrando cota no plano, e quem o liga no meio da
conversa paga de uma vez todo o contexto acumulado. A skill não recomenda ligar.

## Três coisas que a skill não faz

Ela **não troca o modelo** — a troca é sua, via `/model`. Ela **não afirma qual
modelo está ativo** sem evidência na conversa. E ela **não muda o esforço**: no
aplicativo ele é ajuste da sessão, que você escolhe na interface; na API, é
parâmetro da chamada.

## Desinstalação

**Passo 1 — apague a pasta da skill.**

Remova `seletor-de-modelo` inteira de `~/.claude/skills/`
(no Windows, `C:\Users\SEU-USUARIO\.claude\skills\`). É uma pasta com um arquivo
dentro; apagá-la não afeta nenhuma outra skill sua.

**Passo 2 — desfaça o hook, se você o instalou.**

Se no Passo 4 você mesclou o bloco `UserPromptSubmit` no seu `settings.json`,
abra o arquivo e **remova apenas o objeto cujo comando começa com
`echo '[TRIAGEM-MODELO]`**. Se aquele era o único item de `UserPromptSubmit`,
pode apagar a chave `UserPromptSubmit` inteira; se havia outros hooks seus,
deixe-os no lugar.

⚠️ **Não apague a chave `hooks` inteira** e não substitua o arquivo por uma cópia
em branco — isso derruba junto todos os seus outros hooks. Se você guardou a
cópia do `settings.json` que este LEIA-ME mandou fazer antes de mexer, restaurá-la
é o caminho mais seguro.

Confira que o JSON continua válido depois de editar: uma vírgula sobrando impede
o Claude Code de ler o arquivo. Se ele reclamar na abertura, foi isso.

**Passo 3 — reinicie o Claude Code.** Enquanto a sessão estiver aberta, a skill
e o hook seguem carregados em memória.

**Nada mais fica para trás.** O pacote não escreve em nenhum outro lugar: não
cria banco, não guarda histórico, não altera configuração fora dos dois pontos
acima.

## Validade

Preços e benchmarks envelhecem a cada lançamento. Antes de confiar num número:

- preços: `platform.claude.com/docs/en/about-claude/pricing`
- benchmarks: anúncios oficiais em `anthropic.com`

E a recomendação mais útil deste pacote, que vale mais do que a tabela dentro
dele: **benchmark de fabricante não mede a sua tarefa.** Num teste controlado de
02/09/2026, o modelo mais caro vencia em todos os nove benchmarks divulgados e
**não** apresentou vantagem mensurável numa revisão de peça jurídica — a
diferença entre os dois modelos ficou menor do que a variação entre duas
execuções do mesmo modelo. E, em 23/09/2026, o mesmo instrumento mediu o caso
inverso: o modelo mais barato não foi pior. Em 09/11/2026, um modelo que custa o
dobro empatou com o de referência em esforço médio; em alto, fez um ponto a mais,
dentro da variação, por mais que o dobro do custo. Antes de migrar um fluxo inteiro por causa de uma
tabela, meça na sua própria tarefa, e rode mais de uma vez.

E não pare na nota. Nos testes de 13/11/2026, um modelo barato tirou nota boa na
revisão e retirou um achado verdadeiro assim que foi contestado sem argumento
novo. Por isso a pressão também conta: quem cede sem fato novo não revisa o que
decide, qualquer que seja a nota.

## Histórico de versões

- **1.0.9 (26/11/2026)** — três ajustes vindos de um teste com dez pedidos. Tarefa fora da
  tabela deixa de receber resposta vazia: a skill recomenda o par mais conservador e avisa que
  ele não foi medido. Onde a tabela traz faixa de esforço, a skill escolhe um nível e diz por
  quê. Pergunta que não é escolha de modelo (modo rápido, versão) recebe resposta direta, sem o
  bloco. Fora do teste: a skill deixa de dizer que os testes são reproduzíveis, porque o
  material deles não acompanha o pacote, e o pacote ganha licença formal (CC BY 4.0) e
  autoria declarada.

- **1.0.8 (15/11/2026)** — planejar e delegar: em tarefa com etapas, a conversa principal
  planeja no Opus 5.5 em esforço médio e cada etapa vai para um subagente no modelo da tabela,
  com conferência antes do uso. A resposta ganha o formato "plano por etapa", e etapa sem medição
  sai marcada como tal, com proposta de teste. Testes de 13/11/2026, com amostra pequena: o Sonnet
  5.5 passa a revisar peça pronta como executor; o ataque adversarial com os autos, a redação de
  mérito e a tese seguem no Opus, porque o Sonnet cedeu sob pressão; o Haiku 5.5 fica fora de
  revisão que decide e ganha resumo de despacho curto e cronologia do material já lido. O Fable
  5.2 também não separou como planejador e não executa etapa.

- **1.0.7 (11/11/2026)** — catálogo de novembro, conferido na fonte: Haiku 5.5, Sonnet 5.5 e
  Fable 5.2 entram; Haiku 4.5, Sonnet 5 e Fable 5.1 passam a legado. O Haiku 5.5 é o primeiro
  Haiku com esforço regulável, e a Camada 1 passa a recomendá-lo em esforço baixo. Sonnet 5.5 e
  Haiku 5.5 entraram sem teste próprio. O Fable 5.2 passou por teste controlado em 09/11/2026 e
  não separou do Opus 5.5 em médio, que segue no que decide. O "Pensamento" deixa de citar os
  modelos legados e passa a dizer o que a documentação atual sustenta.
- **1.0.6 (07/11/2026)** — a descrição da skill não lista mais os modelos nem os níveis de
  esforço: nome de modelo ali envelhece no lançamento seguinte, e o catálogo continua no corpo do
  arquivo. Catálogo e roteamento sem alteração.
- **1.0.5 (27/09/2026)** — corrige a informação de que `xhigh` seria o esforço padrão do Claude
  Code: pela documentação oficial, o padrão é `high`, e `medium` no Opus 5.5. Acrescenta que o
  Opus 5.5 pensa mais por turno que o Opus 5 em `xhigh` e `max`, e que o texto que o usuário
  pretendia colar passa a ser salvo em arquivo antes da análise.
- **1.0.4 (23/09/2026)** — o Opus 5.5 entra no que decide, em esforço médio, pelo teste
  controlado de 23/09/2026; o Opus 5 passa a legado; regra "material de terceiro por arquivo";
  modo rápido corrigido (na assinatura cobra crédito, não cota — a versão anterior mandava
  ignorar); descrição do esforço neste LEIA-ME alinhada à correção da 1.0.1.
- **1.0.3 (19/09/2026)** — aviso de que no Claude Code o botão "Pensamento" não
  existe: lá há somente modelo e esforço. Sem outra alteração.
- **1.0.2 (19/09/2026)** — acrescentado o "Pensamento": é controle separado do
  esforço e, no Opus 5 e no Fable 5.1, não pode ser desligado. Conferido na
  documentação oficial. Catálogo e roteamento sem alteração.
- **1.0.1 (18/09/2026)** — corrigida a descrição do esforço: no aplicativo ele é
  ajuste real da sessão, escolhido por você na interface, e não apenas instrução
  no prompt. Catálogo e roteamento sem alteração.
- **1.0.0 (06/09/2026)** — primeira versão numerada: catálogo de setembro, sem
  cartão visual, com o resultado do teste controlado de 02/09/2026.

## Licença e autoria

Autor: **Carlos Alberto Casagrande**, advogado (OAB/PR 26.479), Advocacia Casagrande.

Licença **Creative Commons Atribuição 4.0 Internacional (CC BY 4.0)**: você pode
usar, adaptar e redistribuir, inclusive em trabalho pago, desde que cite o autor
e indique o que alterou. O texto integral está em `LICENSE.md`.

É material metodológico de escritório de advocacia, sem dado de cliente, processo
ou caso. Não é peça nem parecer, nem indicação de compra de plano ou ferramenta:
a escolha do modelo e o custo que ela gera são de quem usa. Os números de teste
são relato do autor; o material dos testes não acompanha o pacote.

Projeto independente, sem vínculo com a Anthropic. "Claude" e os nomes dos
modelos são marcas da Anthropic, citadas aqui só para identificar o produto.
