---
title: Dockerizando Shiny Apps
author: Flavio Barros
date: '2015-04-30'
categories:
  - Digital Ocean
  - Docker
  - Estatística
  - Linux
  - R e RStudio
  - Shiny
tags:
  - deploy
  - implantação
  - shiny
  - web
slug: dockerizando-shiny-apps
---

Depois de uma longa pausa de mais de quatro meses, finalmente estou voltando a postar aqui. Infelizmente, diversos compromissos me impediram de continuar postando, mas acabei por dar uma repaginada no blog, alterar a implantação (agora esse blog roda inteiramente dentro de um contêiner docker, com algumas outras coisas legais que pretendo postar mais para frente) e fazer esse post.

Como esse post pode vir a ter leitores com diversos backgrounds eu vou inicialmente falar um pouco sobre várias tecnologias, como a linguagem R, os Apps do Shiny e o Docker.  Vou falar para que servem e o problema que está sendo resolvido aqui. Se você já sabe o que é o R e o Shiny pode pular para o item 2 sobre o Docker.

## 1. R e apps Shiny

Inicialmente o que são Shiny Apps? Bom, quem já trabalha com [R](http://cran.r-project.org/) já deve ter ouvido falar do [Shiny](http://shiny.rstudio.com/). O projeto Shiny é um framework para aplicações web feito especificamente para  a linguagem R. Para quem não conhece, a linguagem R é uma linguagem de programação criada especificamente para estatística/análise de dados e que vem [ganhando muita popularidade](http://www.infoq.com/br/news/2014/10/ranking-linguagens-ieee) no segmento corporativo.

O R já vinha sendo praticamente o padrão em termos de software para Estatística na academia, principalmente por conta de ser um software [open source](http://pt.wikipedia.org/wiki/C%C3%B3digo_aberto). Também vem sendo adotado de forma massiva na indústria, principalmente nos EUA, justamente porque conta com bibliotecas para praticamente qualquer tarefa na área de análise de dados e gráficos. Essas bibliotecas, em sua maioria, foram criadas pelos próprios usuários, e estendem a linguagem de tal forma que é possível produzir [gráficos de altíssima qualidade](http://r4stats.com/examples/graphics-ggplot2/) ou criar modelos de forma [muito simples](http://topepo.github.io/caret/index.html). Existem também grandes empresas investindo na linguagem, como a [Revolution Analytics](http://www.revolutionanalytics.com/), que foi recentemente [comprada pela Microsoft](http://blog.revolutionanalytics.com/2015/04/revolution-analytics-microsoft.html). Essa compra sinaliza o interesse que empresas do porte da Microsoft tem na linguagem R.

Entretanto, um dos pontos fracos do R, é que se trata de uma linguagem de nicho, feita especificamente para análise de dados. Assim, nem sempre era fácil disponibilizar suas análises e resultados na internet de forma interativa. Houve iniciativas nesse sentido, mas no fim das contas, se o usuário queria disponibilizar suas análises na web, ou devia usar meios estáticos, como páginas em HTML e arquivos em PDF, ou criar aplicações com tecnologias como o Javascript, e de alguma forma ligar um [servidor na web](https://rforge.net/Rserve/doc.html) rodando o R a estas aplicações.

Esse tipo de solução funciona, mas exige ou um profissional com conhecimentos sólidos em javascript, infraestrutura e estatística/análise de dados, ou pelo menos uma equipe multidisciplinar. Na prática isso significava que um usuário comum do R teria muitas dificuldades em criar aplicações web a partir das suas análises.

É nesse ponto que entra o Shiny. O Shiny é um freamework web, criado pela equipe do [RStudio](http://www.rstudio.com/), que permite que o usuário do R crie apps web, utilizando somente a própria linguagem. A sobrecarga para o usuário é mínima, tal que este pode desenvolver e rodar suas aplicações localmente de forma muito simples, com um comando como o runApp(). Só para demonstrar como funciona o processo, assista rapidamente a esse vídeo, onde é rodado um app do Shiny pela IDE RStudio.

https://youtu.be/CWWPvA6SK6k

## 2. Docker

Se você de alguma forma acompanha notícias do mundo open source então você provavelmente já ouviu falar do [Docker](http://www.docker.com/). O Docker é uma ferramenta fantástica que permite criar contêineres de software, totalmente isolados do sistema operacional hospedeiro. Ele funciona como uma máquina virtual, mas é simplesmente muito mais leve.

A ideia por trás do docker é que o desenvolvedor cria um contêiner com todas as bilbiotecas e dependêcias que desejar garantindo que tudo funciona. O responsável pela implantação do software não precisa saber o que há dentro do contêiner, ele só precisa ser capaz de rodar contêineres no servidor. Ainda que uma funcionalidade como essa pudesse ser obtida com máquinas virtuais, elas acabavam vindo com muito mais que o necessário, tal que os arquivos eram muito grandes (>>1Gb) e tornavam o sistema hospedeiro muito lento.

O docker por outro lado, não usa um SO completo, ele compartilha o mesmo Kernel do hospedeiro (sim, atualmente ele precisa rodar no Linux) mas é um ambiente completamente isolado. Assim, rodar contêineres do docker é muito, mas muito mais leve que rodar uma máquina virtual. Os recursos do docker não param por ai, ele também permite uma espécie de versionamento de contêineres e tem uma espécie de github para contêineres, o [Docker Hub](https://hub.docker.com/account/signup/), de onde o usuário pode baixar e usar imagens prontas para diversos softwares, como o MySQL, Postgres, LAMP, WordPress, dentre outros. Se você quiser entender melhor o que é o Docker, assista a esse vídeo de um dos fundadores do projeto.

https://www.youtube.com/watch?v=Q5POuMHxW-0

## 3. Dockerizando uma app do Shiny

Na parte 1, eu mostrei um exemplo de um app do Shiny rodando no RStudio localmente. Para desenvolver está ok, mas e se eu quiser disponibilizar para alguém? Uma solução é você enviar os arquivos do projeto, tal que a pessoa que vai receber precisa somente de uma instalação do R com o pacote shiny para rodar a aplicação. Como uma aplicação básica do shiny precisa somente de dois arquivos (ui.R e server.R), você só precisaria enviar esses arquivos.

Mas e se eu quiser colocar na web? Existem basicamente duas alternativas:

1) Preparar um servidor com o [Shiny Server](http://www.rstudio.com/products/shiny/shiny-server/)

2) Usar o serviço [shinyapps.io](http://www.shinyapps.io/)

A opção 1) pode ser muito complicada para alguns usuários, as vezes inviável, por conta da necessidade de instalar e configurar um servidor.

A opção 2) é mais interessante, entretanto pode sair bem caro, uma vez que o plano free pode ser muito limitado para algumas necessidades.

Como o docker pode ajudar? Inicialmente com o docker é possível criar um servidor com o shiny usando um comando. Isso simplifica bastante a implantação de um servidor. Veja neste pequeno vídeo um exemplo:

http://youtu.be/H94eSQrPoYI

Enfim, basta rodar o comando:

    docker run --rm -p 3838:3838 rocker/shiny

que você tem um servidor do shiny rodando na máquina.

**USUÁRIOS DO WINDOWS E MAC:** Vocês vão precisar do [boot2docker](http://boot2docker.io/) instalado para rodar o contêiner localmente. Como eu estou usando o Linux no vídeo, o terminal já está naturalmente disponível, mas se você instalar o [boot2docker](http://boot2docker.io/) vai ter um terminal a disposição para reproduzir o que eu estou fazendo nesse vídeo e no próximo abaixo.

Essa solução parece resolver o problema, pois para disponibilizar seus apps basta criar um servidor e instalar o docker. Entretanto o usuário ainda pode encontrar diversos problemas como:

1) Como colocar meus apps nesse servidor?

2) Como colocar um endereço na web direto para minha aplicação?

3) Como tirar aquele 3838 do meu endereço?

4) Como criar uma imagem docker com meu app?

Foi pensando em resolver esses problemas que eu criei um contêiner de exemplo, com um app pronto, que aparece na barra do navegador assim que a imagem está rodando. Já subi a imagem no Docker Hub, e ela está pronta para você testar e usar. A imagem está disponível [nesse link](https://registry.hub.docker.com/u/flaviobarros/shiny-wordcloud/) e o código fonte está no [Github](https://github.com/flaviobarros/shiny-wordcloud) caso queria fazer seus próprios apps.

Aqui nesses dois vídeos eu mostro como rodar esse contêiner localmente e depois como rodar no [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6). Primeiro localmente:

http://youtu.be/nhslu5WZy6I

E agora no Digital Ocean:

https://youtu.be/X68N_efY0KU

 É importante observar que no vídeo, ao usar o comando citado, você não volta direto ao terminal, tal que seria necessário um Ctrl + C para encerrar o contêiner. Assim, para manter o contêiner rodando e voltar ao terminal, você deve utilizar o & no final do comando.

    docker run --rm -p 80:80 flaviobarros/shiny &

enfim, é possível testar localmente com um comando e também é possível rodar na web imediatamente com um servidor e o docker instalado. Veja que eu usei o ip do servidor no navegador, mas é bem simples apontar um domínio para não precisar do ip.

Você pode rodar esse app na [Amazon Web Services](http://aws.amazon.com/pt/), no [Google Cloud](https://cloud.google.com/) e na [Microsoft Azure](http://azure.microsoft.com/pt-br/), já que todos eles já oferecem suporte ao docker. Entretanto minha sugestão é que você comece com o [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6) que é muito mais fácil de usar.

IMPORTANTE: acessando qualquer link dessa página para o [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6), ao fazer o cadastro você vai ganhar U$10,00 de crédito sem compromisso de manter o serviço. Com esse crédito você poderá manter um VPS simples, com 512Mb de RAM, por dois meses de graça! Use os links desse post.
