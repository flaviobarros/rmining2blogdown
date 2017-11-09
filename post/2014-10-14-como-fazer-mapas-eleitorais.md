---
title: Como fazer mapas eleitorais
author: Flavio Barros
date: '2014-10-14'
categories:
  - R e RStudio
tags:
  - ggplot2
  - mapas
slug: como-fazer-mapas-eleitorais
---

Se você vota no Aécio e chegou aqui por meio dessa figura, calma, não tenha um derrame, os dados são fictícios! Para descontrair um pouco do clima das eleições, hoje eu vou mostrar como criar mapinhas eleitorais. Esses mapinhas são muito interessantes, e além de dados de votação, você pode incluir qualquer tipo de dados que quiser nesses mapas. Eu escolhi o mapa da cidade de São Paulo para mostrar como é possível fazer mesmo os mapas com maior granularidade, desde que você possuas os [shapes](http://pt.wikipedia.org/wiki/Shapefile) do mapa que quiser fazer.

<!-- more -->

![sampa](http://www.flaviobarros.net/wp-content/uploads/2014/10/sampa.png)Para construir esse mapa eu vou utilizar o [ggplot2](http://ggplot2.org/) e o [maptools](http://r-forge.r-project.org/projects/maptools/), mas tenho certeza que é possível fazer de outras formas com outros pacotes. Particularmente eu gosto muito de usar o ggplot2 pela controle que você tem sobre os aspectos do mapa e também pela qualidade do trabalho final. Além disso o ggplot2 conta com funções específicas para a criação de mapas tal que eu considero esta uma das melhores maneiras de construir esses mapinhas no R.

Inicialmente, para criar qualquer mapa, precisamos do shapefile que queremos plotar (existem outros formatos, mas aqui vamos usar shapefiles). Como eu queria o shape da cidade de São Paulo, eu fui no [site da prefeitura](http://downloadfolhasscm.prefeitura.sp.gov.br/PaginasPublicas/index.aspx) e me cadastrei. Ao entrar no sistema você vai encontrar uma interface com um mapa da cidade; no canto superior esquerdo tem um botão com um mapinha de São Paulo e uma seta para baixo, passando o mouse sobre ele você vai ver algo como "Download camadas município" e ao clicar vai aparecer uma janela onde você seleciona a camada que deseja baixar. Para reproduzir esse mapa baixe a camada "Distrito Municipal" tal que será baixado o arquivo DISTRITO.zip. Descompacte-o na pasta onde você vai rodar o script em R e siga os passos a seguir.

Inicialmente vamos ler o shape e plotar um mapinha cru, sem nada, só para você ver como eles ficam sem nenhum tipo de informação:

```r
## Carregando pacotes
library(ggplot2)
library(maptools)

## Lendo o mapa de São Paulo
sampa <- readShapeSpatial('DISTRITO/DISTRITO.shp')
sampa.fort <- fortify(sampa, region='NOME_DISTR')

## Criando um mapa cru
ggplot() +
+ geom_path(data=sampa.fort, aes(x=long, y=lat, group=group), colour="black", size=0.25)
```

![mapa_cru](http://www.flaviobarros.net/wp-content/uploads/2014/10/mapa_cru.png)

Veja que ele apresenta somente as formas, com um grid e a latitude e longitude nos eixos. A partir desse mapinha, vamos adicionar dados externos ao mapa e relaciona-los às informações presentes no shapefile. Para isso, vamos usar o id do arquivo do mapa e criar dados eleitorais fictícios indexados pelo mesmo id. O segredo para incluir os dados externos no mapa é associar esse ids. Veja que ao utilizarmos a função fortify() do ggplot2, ele cria internamente um data.frame indexado pelo id do shapefile. Se eu criar um conjunto de dados que utiliza os mesmos ids, quando eu passa-lo ao ggplot2, ele será capaz de associar as informações corretamente e apresenta-las no mapa. Se você verificar a estrutura do objeto sampa.fort que acabamos de criar você vai ver as seguintes informações:

    str(sampa.fort)
    'data.frame':   36614 obs. of  7 variables:
     $ long : num  338651 338616 338608 338606 338618 ...
     $ lat  : num  7392203 7392245 7392262 7392276 7392300 ...
     $ order: int  1 2 3 4 5 6 7 8 9 10 ...
     $ hole : logi  FALSE FALSE FALSE FALSE FALSE FALSE ...
     $ piece: Factor w/ 1 level "1": 1 1 1 1 1 1 1 1 1 1 ...
     $ group: Factor w/ 96 levels "AGUA RASA.1",..: 1 1 1 1 1 1 1 1 1 1 ...
     $ id   : chr  "AGUA RASA" "AGUA RASA" "AGUA RASA" "AGUA RASA" ...

veja que id indexa os distritos municipais, portanto será a partir deles que iremos associar os dados externos. Vamos criar os dados:

```r
## Gerando dados para mapa eleitoral
dados <- data.frame(id = unique(sampa.fort$id))
dados$percent <- runif(n=nrow(dados), min=0, max=1)
dados$partido <- ifelse(dados$percent > 0.25, 'PT', 'PSDB')
dados$partido <- factor(dados$partido, c('PT', 'PSDB'))
```

 Por fim, agora basta incluir uma camada de dados no mapa, com a função geom_map(), que o mapa será criado de acordo com a primeira figura do post. Veja que utilizamos diversos controles da função theme() do ggplot2 para retirar os eixos, o grid, os títulos e etc, tal que o mapa ficou somente com as formas, as cores e uma legenda.

```r
## Gerando o mapa
ggplot() +

  ## Cria o mapa associando informações dos dados
  geom_map(data=dados, aes(map_id=id, fill=partido), map=sampa.fort) +

  ## Aqui entram os dados do mapa
  geom_path(data=sampa.fort, aes(x=long, y=lat, group=group), colour="black", size=0.25) +

  ## Aqui retiramos o grid, títulos e etc.
  theme(panel.background = element_blank(),
        axis.title.x = element_blank(),
        axis.title.y = element_blank(),
        axis.text.x = element_blank(),
        axis.text.y = element_blank(),
        axis.ticks = element_blank(),
        panel.grid.major = element_blank(),
        panel.grid.minor = element_blank())
```

Se você quiser realmente incluir informações eleitorais reais, você vai precisar baixar os [dados da votação do TSE](http://www.tse.jus.br/eleicoes/eleicoes-2014/divulgacao-de-resultados-das-eleicoes-2014). Já vou avisando que os dados estão um caos, e além de trata-los você ainda vai precisar relacionar as zonas eleitorais com os distritos municipais. Perceba que um distrito municipal não é necessariamente um só bairro, alguns distritos englobam diversos bairros. Se você tiver acesso a uma listagem das zonas eleitorais por distrito em São Paulo, por favor me envie pois aí poderemos refazer alguns desses mapas com as informações reais.
