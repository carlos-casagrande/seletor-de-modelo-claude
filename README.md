# Seletor de Modelo e Esforço para Claude Code

Uma *skill* para o Claude Code que responde a uma pergunta de todo dia: **qual
modelo Claude usar nesta tarefa, e com quanto esforço?** Foi escrita para o
trabalho jurídico brasileiro e nasceu do uso diário num escritório de advocacia.

Ela recomenda; quem troca o modelo é você.

## O que ela faz

Você pergunta em linguagem normal ("isso precisa de Opus?", "dá para economizar
aqui?") e recebe um bloco curto:

```
**Tarefa:** Triagem de intimações e identificação de prazos.
**Recomendo:** Sonnet 5.5 @ low
**Porquê:** Volume e latência; a saída é conferível item a item e não decide mérito.
**Trocar:** CLI → /model claude-sonnet-5-5
**Economia:** Real — é a faixa de maior volume do escritório.
```

Em tarefa com várias etapas, ela devolve um plano: a conversa principal planeja
e confere, e cada etapa vai para um subagente no modelo mais barato que aquela
etapa admite.

Regra geral: economize no rotineiro e conferível; no que vai a protocolo ou
decide estratégia, suba modelo e esforço.

## Instalação

1. Baixe o zip da versão mais recente na aba **Releases** (ou copie a pasta
   `skills/seletor-de-modelo` deste repositório).
2. Coloque a pasta `seletor-de-modelo` em `~/.claude/skills/`
   (no Windows, `C:\Users\SEU-USUARIO\.claude\skills\`).
3. Reinicie o Claude Code e pergunte: *"Qual a versão da skill seletor-de-modelo?"*

O passo a passo completo, o hook opcional de pausa, a desinstalação e o histórico
de versões estão no [LEIA-ME.md](LEIA-ME.md).

## O que você precisa saber antes de confiar

- **Os preços e os modelos envelhecem.** O catálogo é de novembro de 2026.
  Confira na página oficial de preços da Anthropic antes de citar número.
- **Os testes são relato do autor.** A skill cita medições feitas no escritório,
  com amostra pequena. O material desses testes não está neste repositório, então
  você não tem como reproduzi-los daqui. Trate como indício e meça na sua tarefa.
- **A tabela reflete um escritório** de criminal, execução penal, cível e
  família. Adapte as linhas ao que você faz.

## Licença e autoria

Autor: **Carlos Alberto Casagrande**, advogado (OAB/PR 26.479), Advocacia Casagrande.

Licença [Creative Commons Atribuição 4.0 Internacional (CC BY 4.0)](LICENSE.md):
use, adapte e redistribua, inclusive em trabalho pago, citando o autor e
indicando o que alterou.

Sem dado de cliente, processo ou caso. Não é peça nem parecer.

Projeto independente, sem vínculo com a Anthropic. "Claude" e os nomes dos
modelos são marcas da Anthropic, citadas aqui só para identificar o produto.
