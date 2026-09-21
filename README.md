# triagemLivre

Ferramenta de apoio à execução de um **mapeamento sistemático da literatura**, rodando
inteiramente na sua máquina. Sem conta, sem servidor, sem nuvem — mesma linha do
[LivreAnalise](https://github.com/Lucas-Tito/LivreAnalise).

> **Status: nada implementado.** Este repositório hoje é só este documento: o registro
> do que a ferramenta vai ser, do que ficou em análise e do que foi descartado, com o
> motivo de cada decisão.

## O problema

Um mapeamento sistemático não é difícil, é volumoso. Depois de executar a string de
busca em três bases (ACM, IEEE Xplore e Scopus), sobram alguns milhares de registros
para triar por título e resumo — e cada registro passa por **dois revisores
independentes**, que só depois comparam as decisões.

Na escala de ~7 mil registros e 3 pessoas, cada revisor vê cerca de 65% do total. A
vinte segundos por registro, são de 25 a 35 horas por pessoa, só de triagem. É aí que
está o custo do mapeamento inteiro — não na extração de dados, que envolve uma centena
de artigos.

O Parsifal cobre o processo formal. A ferramenta aqui não tenta substituí-lo: ataca os
pontos em que a execução real dói.

## O que a ferramenta faz

### Relatório de duplicatas — acusa, não funde

Recebe os exports das três bases e aponta quais registros são o mesmo trabalho: DOI
idêntico, ou título quase idêntico entre bases diferentes.

**Nada é reescrito.** A alternativa seria fundir os arquivos e gerar um export novo,
já limpo — descartada porque você perde de qual base cada registro veio e passa a
confiar num arquivo que ninguém conferiu. O que sai é um relatório com os pares e a
evidência de cada um; a decisão e a ação continuam sendo suas.

O Parsifal já remove a duplicata **exata**. O que escapa dele é a aproximada: mesmo
artigo com pontuação diferente no título, versão de workshop e de periódico, preprint
e publicado. É essa que o relatório pega. De quebra, o total de duplicatas removidas é
um dos números exigidos pelo fluxograma PRISMA.

### Exportação de contexto para IA, por escopo

Mesmo desenho do LivreAnalise: a ferramenta **não chama API nenhuma**. Ela monta um
arquivo de texto com o material que você escolher, e você cola onde quiser.

O escopo é seu:

- só os títulos
- título + resumo
- só os registros ainda não triados
- só os registros em que os dois revisores divergiram
- um lote de N registros por vez, para caber na janela do modelo

O arquivo sai com o critério de inclusão e a delimitação de escopo junto, para a
resposta ser sobre o seu protocolo e não sobre transparência em geral.

### Parecer da IA como terceiro revisor

A resposta da IA volta para dentro da ferramenta por importação, e aparece **ao lado**
das decisões humanas, com a justificativa e o trecho do resumo que a motivou.

Ela nunca decide. O valor é outro: ordenar a fila por relevância provável, permitir
excluir em bloco o ruído previsível (a literatura de "explainable AI", que a string
arrasta às centenas), e destacar os casos em que os dois revisores concordaram mas a
IA discorda — que é onde vale gastar atenção.

## Em análise

### Triagem por teclado

Uma tela que mostra um registro por vez — título, resumo, ano, veículo, nada mais — e
decide no teclado: uma tecla inclui, outra exclui pelo critério correspondente, e a
tela já pula para o próximo. Sem clique, sem confirmação, sem esperar página carregar.
Cada revisor no seu arquivo, sem ver a decisão do outro; depois, um comando funde os
dois e mostra só as divergências.

É o que mais economizaria tempo: a diferença entre clicar numa página web e apertar uma
tecla, multiplicada por milhares de registros e dois revisores.

**Por que está em análise, e não decidido:** é também o que mais se sobrepõe ao
Parsifal, que já é a ferramenta oficial do processo. Adotar isso significa manter os
dois em sincronia. E a conta só fecha no volume alto — se a calibração da string
enxugar o resultado para cerca de 2 mil registros, não vale construir.

## Fora de escopo, por ora

**Formulário de extração e análise.** Depois da triagem vem a leitura integral e o
preenchimento de um formulário por estudo, e daí saem as contagens e os gráficos de
mapeamento. É trabalho de meses à frente, o Parsifal já oferece o formulário, e
construir agora seria adiantar algo que talvez não precise existir.

**Descartados de vez:**

- *Download automático de PDF pelo proxy da rede federada* — quebra a cada mudança do
  proxy e provavelmente fere os termos das editoras.
- *OCR* — artigo das três bases já vem com texto.
- *Snowballing automatizado* — o protocolo não prevê snowballing.
- *Busca semântica por embeddings* — o método exige busca reproduzível por string; um
  achado que só a semântica encontrou não tem como entrar no PRISMA.

Nenhum dos quatro toca no gargalo real, que é passar o olho em milhares de títulos.

## Princípios

Herdados do LivreAnalise, e a razão de a ferramenta existir em vez de ser uma planilha:

- **Roda local.** Nenhum dado de pesquisa sai da máquina por conta da ferramenta.
- **O projeto é um arquivo.** Sem conta, sem servidor, sem sincronização.
- **A IA entra por exportação manual.** Você vê exatamente o que sai antes de sair, e
  escolhe se envia.
- **A IA nunca decide.** Ela opina; a inclusão e a exclusão são da dupla de revisores.
