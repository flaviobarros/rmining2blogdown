---
title: Usando R com o MonetDB
author: Flavio Barros
date: '2016-10-15'
categories:
  - MonetDB
  - R e RStudio
  - SGBD
slug: usando-r-com-o-monetdb
---

Quem já trabalha com a linguagem R há um certo tempo provavelmente já está ciente das limitações da linguagem com relação a conjuntos de dados maiores que a memória RAM. Esse é um problema complicado que está associado com o fato de que quando o R cria um objeto, esse objeto é carregado inteiro na memória. Então, caso o usuário esteja efetuando a leitura de um conjunto de dados, a menos que esse conjunto "caiba" na memória, não será possível utiliza-lo dentro do R. Em muitos problemas comumente encontrados em estatística elementar, como analisar dados de experimentos, realizar alguns testes de hipóteses, criar visualizações, dentre outros, isso não é uma limitação pois os objetos são muito menores que a memória RAM do computador. ENTRETANTO, nessa era do "big data" não é difícil encontrar situações onde o usuário acaba  tendo problemas sérios com essa dificuldade do R. Umas destas situações sendo a análise de diversos conjuntos de dados públicos, como o Censo Demográfico, ENEM, Censo Escolar, RAIS e etc.

Mas como resolver esse problema? Há uma [questão](http://pt.stackoverflow.com/questions/30631/estrat%C3%A9gias-para-analisar-bases-de-dados-muito-grandes-em-r-que-n%C3%A3o-caibam-na-m/34004#34004) no stackoverflow onde foi perguntado exatamente isso, e eu como um dos participantes forneci minhas apreciações sobre o tema. Basicamente, em situações onde o conjunto de dados é "manejável" dentro de um PC comum, mas é maior que a RAM, o ideal é usar um pacote como o [ff](https://cran.r-project.org/web/packages/ff/index.html) ou [bigmemory](https://cran.r-project.org/web/packages/bigmemory/index.html) ou mesmo utilizar um SGBD. Eu diria que quando estamos trabalhando com conjuntos de dados que superam bastante o tamanho da memória RAM, para mim, o ideal é sempre trabalhar com sistemas de gerenciamento de banco de dados, como o PostgreSQL, o MySQL ou o MonetDB. Especialmente com microdados públicos, que em geral você só precisa ler uma vez e a partir daí só consultar. Além de permitir o armazenamento consistente dos dados, também é possível utilizar o pacote dplyr no R, com o [backend de bancos de dados](https://cran.r-project.org/web/packages/dplyr/vignettes/databases.html), tal que você pode utilizar as tabelas do banco utilizando praticamente a mesma sintaxe que utilizaria com um data.frame. É uma solução que é rápida, não tem as limitações que o R têm com RAM e que você pode utilizar depois por muito tempo.

## Qual utilizar?

De cara recomendo o PostgreSQL e o MySQL. São open source, amplamente utilizados e documentados e tem diversos recursos que se espera em um SGBD. ENTRETANTO HÁ UM PROBLEMA: você precisa instala-los e configura-los. Pode não parecer um grande problema mas o fato é que isso, por si, já impõe uma séria barreira para muitos usuários do R. É uma barreira também por conta de que muitos usuários que não precisam de todos os recursos que sistemas como esse oferecem podem não investir em uma solução como essa pelo trabalho extra necessário para resolver um simples problema com a manipulação de arquivos maiores que a RAM. Porém há alternativas! Dos SGBDs disponíveis, dois que não dão nenhum trabalho de usar com R, não precisando ter que instalar nenhum outro software além do prórprio R, são:

  * [SQLite3](https://www.sqlite.org/);

  * [MonetDB](https://www.monetdb.org/Home);

O primeiro pode ser utilizado facilmente a partir do R por meio do pacote [RSQlite](https://cran.r-project.org/) e o segundo por meio do pacote [MonetDBLite](https://cran.r-project.org/). O meu preferido, e o que eu vou utilizar aqui como exemplo, é o MonetDB. Vou utilizá-lo porque dos dois é aquele que faz o armazenamento por [colunas](https://en.wikipedia.org/wiki/Column-oriented_DBMS). ASSIM, a operação de escrita é um pouco mais cara, mas qualquer operação de consulta é muito mais barata. Se você trabalha com dados que não precisam ser escritos com frequência, mas precisam ser lidos com frequência, sistemas de gerenciamento de banco de dados colunares são superiores. Por outro lado, se além de ler você também precisa escrever no banco frequentemente, um banco como o SQLite (e outros) deve ser superior. Especificamente no caso de microdados, como Censo, ENEM ou Censo Escolar, que você deve carregar o banco uma vez e somente ler a partir daí, acredito que o MonetDB é a melhor opção disponível hoje.

## Aplicação

Para utilizar a solução que vou propor aqui você deve ter o pacote MonetDBLite instalado no seu computador. No console do R digite:

```r
install.packages('MonetDBLite', dependencies = TRUE)
```

A partir daí, o próximo passo será carregar os dados no banco. Felizmente o MonetDBLite é um pacote que permite fazer isso de forma automática utilizando a função monetdb.read.csv(). Supondo que você está com o conjunto de dados do [ENEM](http://download.inep.gov.br/microdados/microdados_enem2014.zip), no meu caso o microdados_enem2014.csv, no mesmo diretório de trabalho onde você vai rodar o script, execute os seguintes comandos:

```r
## Carregando os pacotes necessários
library(MonetDBLite)
library(DBI)

## Definindo um diretório
dbdir <- 'database/'

## Criando a conexão com um banco, criado na pasta database
con <- dbConnect( MonetDBLite::MonetDBLite() , dbdir )

## Fazendo a ingestao do csv no banco
monetdb.read.csv(conn = con, files = 'microdados_enem2014.csv', tablename = 'enem2014', header = TRUE, na.strings = '', delim = ',')

## Listando as tabelas no banco
dbListTables(con)

## Contanto o número de registros no banco
dbGetQuery(con, 'SELECT count(*) FROM enem2014')

## Consultando as 100 primeiras linhas
teste <- dbGetQuery(con, "SELECT * FROM enem2014 LIMIT 100")
```

O carregamento não demorou nem um minuto aqui na minha máquina, core i5 e 16Gb de RAM. Mas acredito que em uma máquina mais modesta deve demorar um pouco mais. Outro ponto fundamental é que o ideal é que o arquivo esteja em UTF-8 para o carregamento, e os csv's do ENEM estão em ISO-8859-1. Eu converti o arquivo facilmente por meio do comando iconv no terminal do Linux:

    iconv -f ISO-8859-1 -t UTF-8 MICRODADOS_ENEM_2014.csv > microdados_enem2014.csv

mas no Windows você deve seguir o procedimento de instalação do iconv mostrado [aqui](https://dbaportal.eu/2012/10/24/iconv-for-windows/). De fato há várias maneiras de mudar a codificação de arquivos no Windows e essa é somente uma sugestão.

Outro ponto a salientar é que como o banco e a tabela no banco já foram criados, você pode fazer consultas diretamente em SQL, como se estivesse no terminal do banco. Se você souber SQL seu problema está resolvido e basta gerar os dados necessários a partir da consulta e depois processá-los como data.frame no R.

## Usando o dplyr

Naturalmente a melhor opção para usar bancos de dados com o R é utilizando o dplyr diretamente, o que permite ao usuário do R trabalhar com o banco sem escrever uma única linha de SQL. O mais legal dessa estratégia é que o dplyr converte os comandos em R para querys em SQL e até os resultados intermediários das consultas ficam dentro do banco de dados, tal que não há problemas de performance relacionados as limitações do R com a memória RAM.

Como um exemplo, vamos consultar a nota média dos alunos por estado e dependência administrativa, usando o dplyr.

```r
## Carregando o pacote
library(dplyr)

## Ligando o dplyr na tabela
my_db <- MonetDBLite::src_monetdb(embedded=dbdir)
my_tbl <- tbl(my_db, "enem2014")

## Obtendo média de matemática por estado e dependência administrativa
consulta <- my_tbl %>% group_by(COD_UF_ESC, ID_DEPENDENCIA_ADM_ESC) %>% summarise(mean(NOTA_MT))

## Salvando a consulta como um data.frame
consulta <- collect(consulta)
```

o que resulta em:

      COD_UF_ESC ID_DEPENDENCIA_ADM_ESC       L1
            <int>                  <int>    <dbl>
    1          NA                     NA 472.1223
    2          26                      2 441.0514
    3          32                      2 454.2741
    4          35                      2 468.1310
    5          11                      2 440.4378
    6          33                      4 554.1954
    7          33                      2 456.5003
    8          23                      2 436.1984
    9          29                      2 433.5961
    10         53                      4 447.5004

Veja que será necessário converter os códigos das Unidades da Federação e da dependência administrativa para os respectivos nomes, que estão disponíveis no dicionário que vem com os dados do ENEM. Outro ponto é que ao final da consulta, para "salvar" o resultado da consulta como um data.frame no R você deve usar a função collect().

Por fim, ao terminar de utilizar o banco, você pode desconectar e desligar a instância do MonetDB que foi criada e está rodando na sua máquina:

```r
## Desconectando do banco
dbDisconnect(con, shutdown=TRUE)
```

fique tranquilo que seus dados estão intactos. Se você precisar usar o banco depois basta reconectar como já fizemos anteriormente:

```r
## Criando a conexão com um banco, criado na pasta database
con <- dbConnect( MonetDBLite::MonetDBLite() , dbdir )
```

## Conclusão

Esta é uma solução excelente quando o analista pode consultar um grande banco, filtrar conjuntos menores e depois analisa-los dentro do R. Também é uma forma econômica de manter grandes conjuntos de dados armazenados para futuras consultas em diversos projetos. ENTRETANTO não é a solução indicada quando você precisa criar modelos diretamente no SGBD. Sendo o caso, o ideal é utilizar outros tipos de soluções.
