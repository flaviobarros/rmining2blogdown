---
title: Leitura da PNAD 2013 com o R
author: Flavio Barros
date: '2014-09-24'
categories:
  - Estatística
  - R e RStudio
tags:
  - data.table
  - IBGE
  - PNAD
slug: leitura-da-pnad-2013-com-o-r
---

Com o [erro recente](http://epocanegocios.globo.com/Informacao/Dilemas/noticia/2014/09/ibge-pnad-2013-contem-erros-extremamente-graves.html) na divulgação dos resultados da PNAD 2013, o nome do IBGE e também os resultados dessa pesquisa, chegaram na grande mídia de um forma muito negativa. Ainda assim, a maioria das pessoas desconhece o que é a PNAD, como esses dados são obtidos e como eles podem ser baixados e utilizados. Neste post vou fornecer uma breve explicação do que é PNAD, como esses dados são distribuídos (na forma de microdados) e como você pode facilmente obtê-los e utiliza-los a partir de ferramentas gratuitas como o [R](http://cran.r-project.org/).

<!-- more -->

![pnad2013](http://www.flaviobarros.net/wp-content/uploads/2014/09/pnad2013.jpg)

Vamos à primeira questão: o que é a PNAD? A PNAD é uma sigla para Pesquisa Nacional por Amostra de Domicílios. Segundo o IBGE: "Obtém informações anuais sobre características demográficas e socioeconômicas da população, como sexo, idade, educação, trabalho e rendimento, e características dos domicílios, e, com periodicidade variável, informações sobre migração, fecundidade, nupcialidade, entre outras, tendo como unidade de coleta os domicílios. Temas específicos abrangendo aspectos demográficos, sociais e econômicos também são investigados." É uma pesquisa muito importante para o país, pois é partir dela que obtemos diversos indicadores sobre a população, a economia, a educação, dentre outros. Mas, no entanto, como o próprio nome da pesquisa indica, a pesquisa é feita por amostra, tal que nem todos os domicílios do país são visitados. Na PNAD 2013 por exemplo, foram visitados somente 148.697 domicílios de mais de [57 milhões](http://www.censo2010.ibge.gov.br/sinopse/index.php?dados=P12&uf=00) (Censo 2010).

Mas como essa amostra é escolhida? Como uma amostra tão pequena, de menos de 0,3% do total de domicílios, pode representar dados confiáveis da população? A resposta a essa questão está relacionada à [teoria de amostragem](http://www.amazon.com/Sampling-Techniques-3rd-William-Cochran/dp/047116240X/ref=sr_1_3?ie=UTF8&qid=1411586961&sr=8-3&keywords=sampling+theory), algo que não vou tratar nesse post, mas vou apenas dizer que é possível, com a metodologia certa, obter informações confiáveis da população a partir de uma amostra tão pequena. O IBGE, além de disponibilizar os dados, também disponibiliza o [procedimento amostral utilizado](http://www.ibge.gov.br/home/estatistica/populacao/trabalhoerendimento/pnad2012/microdados.shtm). No link citado, ao baixar o arquivo Metodologia.zip, na pasta descompactada, no arquivo "Notas Metodológicas Pesquisa Básica  2013.doc", o processo de seleção da amostra é apresentado de forma detalhada. Basicamente , é uma amostra probabilística de domicílios, realizada em três estágios:

1) no primeiro estágio  os municípios são classificados em duas categorias, autorrepresentativos e não autorepresentativos, isto é, aqueles municípios que COM CERTEZA vão fazer parte da amostra e aqueles que PODEM fazer parte da amostra. Os municípios não autorepresentativos passam então por um processo de estratificação, e em cada estrato são selecionados alguns municípios, COM REPOSIÇÃO e proporcionalmente à população residente, de acordo com o Censo 2010.

2) No segundo estágio, nos municípios escolhidos no primeiro estágio, são selecionados os [setores censitários](http://www.censo2010.ibge.gov.br/sinopseporsetores/), com reposição e com probabilidade proporcional à população residente de acordo com o Censo 2010.

3) Por fim, no último estágio, são selecionados com EQUIPROBABILIDADE, em cada setor censitário amostrado no segundo estágio, as unidades domiciliares.

Assim, após a coleta, o IBGE compila e oferta estes dados na forma de microdados. Mas o que são os microdados? Veja o seguinte exemplo:

    20131100001500111                                                                 1228091101011.00000000000090.01297567000023.0001733261200                 019101100010000001                 20140919
    20131100001500210                                                                 1228091101011.00000000000090.01297567000023.0001733261200                 019101100010000001                 20140919

Como pode-se ver, são os dados crus, sem uma separação clara entre os campos, tal que para a leitura dos microdados é necessário um dicionário que informa o início de cada campo, o tamanho do campo e qual é a variável. Além disso é necessário ter acesso ao questionário utilizado e um descritor das variáveis. Todas estas informações estão disponíveis no arquivo [Dados.zip](ftp://ftp.ibge.gov.br/Trabalho_e_Rendimento/Pesquisa_Nacional_por_Amostra_de_Domicilios_anual/microdados/2013/Dados.zip), tal que a partir dele é possível reproduzir o procedimento de leitura que eu vou apresentar.

Inicialmente, faça o download destes aquivos, e nas planilhas "Dicionário de variáveis de domicílios da Pesquisa Básica - 2013.xls"  E "Dicionário de variáveis de pessoas da Pesquisa Básica - 2013", exporte para um CSV as três primeiras colunas: Posição Inicial, Tamanho, Código de variável. No meu caso salvei como dicdom.csv e dicpes.csv. Como você vai verificar, existem dois arquivos separados, um para os domicílios e outro para as pessoas (DOM2013.txt e PES2013.txt), tal que em cada domicílio pode haver informações relativas a uma pessoa (único morador) ou mais. Você vai notar que o arquivo de pessoas é razoavelmente maior.

Supondo que você está usando o R em um determinado diretório, e a pasta com os microdados, chamada nesse caso de Dados, está nesse mesmo diretório, execute o script:

```r
library(bit64)
library(data.table)
library(descr)
library(reshape)
library(survey)
library(xlsx)

## Criando o dicionário a partir das três primeiras colunas da planilha
dicdom <- read.csv(file = 'dicdom.csv', header=F)
dicdom <- dicdom[complete.cases(dicdom),]
colnames(dicdom) <- c('inicio', 'tamanho', 'variavel')

dicpes <- read.csv(file = 'dicpes.csv', header=F)
dicpes <- dicpes[complete.cases(dicpes),]
colnames(dicpes) <- c('inicio', 'tamanho', 'variavel')

## Parâmetro com o final de cada campo
end_dom = dicdom$inicio + dicdom$tamanho - 1
end_pes = dicpes$inicio + dicpes$tamanho - 1

## Converte o microdado para um arquivo csv
fwf2csv(fwffile='Dados/DOM2013.txt', csvfile='dadosdom.csv', names=dicdom$variavel, begin=dicdom$inicio, end=end_dom)
fwf2csv(fwffile='Dados/PES2013.txt', csvfile='dadospes.csv', names=dicpes$variavel, begin=dicpes$inicio, end=end_pes)

## Efetua a leitura do conjunto de dados com o fread do data.table
dadosdom <- fread(input='dadosdom.csv', sep='auto', sep2='auto', integer64='double')
dadospes <- fread(input='dadospes.csv', sep='auto', sep2='auto', integer64='double')
```

e pronto! Segundos depois você vai ter lido todos os dados referentes aos domicílios e as pessoas. Veja que a leitura é muito rápida, e este procedimento é uma melhora de outra solução que você pode verificar [aqui](http://www.asdfree.com/search/label/pesquisa%20nacional%20por%20amostra%20de%20domicilios%20%28pnad%29).

Qualquer dúvida com relação ao procedimento, entre em contato por meio dos comentários, e boas análises!
