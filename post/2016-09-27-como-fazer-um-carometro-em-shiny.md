---
title: Como fazer um carômetro em Shiny
author: Flavio Barros
date: '2016-09-27'
categories:
  - Git &amp; Github
  - R e RStudio
  - Shiny
slug: como-fazer-um-carometro-em-shiny
---

Eu venho utilizando o Shiny há um bom tempo. Eu acho que é uma tecnologia fantástica que permite aos usuários da linguagem R criarem pequenas aplicações web que expões scripts, pacotes, funções e diversos outros produtos baseados em dados, sem a necessidade de aprender Javascript, CSS e etc. De fato o que o Shiny faz não é ciência de foguete, mas o público que utiliza o R, em sua maioria, não são programadores web, tal que com essa tecnologia eles podem criar pequenas aplicações imediatamente.

## Carômetro

Eu não sei ao certo onde surgiu esse termo, mas é utilizado em geral como um repositório de informações de pessoas com uma foto associada, com a "cara" das pessoas. Eu que já fui professor em escolas da educação básica e já vi em várias delas o uso do carômetro para identificação dos alunos em conselhos. Mais recentemente ele vem sendo bastante utilizado na política e de fato há vários exemplos em várias linguagens de como fazer [carômetros](https://github.com/germanocorrea/carometro).

## Carômetro no Shiny

Quando eu tive a ideia de construir esse carômetro em Shiny eu fui procurar na internet achando que seria algo trivial de fazer. De fato, não que seja algo muito complicado como em breve você vai ver, mas não encontrei nada pronto e também não me foi imediato qual a melhor estratégia usar. O que eu fiz então? Basicamente eu utilizei o [ShinyDashboard](https://rstudio.github.io/shinydashboard/), coloquei as imagens dentro de um box() e adicionei todas as informações necessárias, salvas em um arquivo de dados com as informações de cada deputado.

O código no Github está disponível aqui:

<https://github.com/flaviobarros/carometro>

## Detalhes

Para quem não tem experiência com o Shiny eu vou salientar dois recursos que eu utilizei. O primeiro foi com relação a filtragem dos dados da tabela deputados. No excerto de código a seguir, que está no arquivo [server.R](https://github.com/flaviobarros/carometro/blob/master/server.R), você vai ver que eu crio uma função para a tabela, baseada nos inputs das caixas de seleção, e sempre que preciso apresentar os dados dos deputados eu me refiro ao função deputados(), tal que garanto que a filtragem é executada toda vez, mantendo a apresentação atualizada.

```r
## Observer
  deputados <- reactive({
    load("./data/deputados.rda")
    deputados <- deputados %>%
      filter(voto %in% input$voto)
  })
```

Esse é um padrão chamado observer, muito útil nesse tipo de situação.

O segundo recurso que eu utilizei nesse app, e que pode parecer bem estranho, é que parte da UI está dentro do [server.R](https://github.com/flaviobarros/carometro/blob/master/server.R). Eu fiz isso porque desde a primeira apresentação, essa parte da UI DEPENDE DOS DADOS FILTRADOS dos deputados. Essa apresentação depende da função deputados(), logo eu declarei um espaço para os boxes no ui.R mas a renderização de fato ocorre dentro do server, depois que os dados de deputados são filtrados.

## Preview

Segue um gif com o preview da aplicação.

![carometro](https://www.rmining.net/wp-content/uploads/2016/09/anim.gif)

Ela também se encontra disponível no shinyapps.io: <https://flaviobarros.shinyapps.io/carometro/>
