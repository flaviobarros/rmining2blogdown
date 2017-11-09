---
title: R & RStudio no Docker com 2 cliques!
author: Flavio Barros
date: '2015-08-21'
categories:
  - Docker
  - Estatística
  - Linux
  - R e RStudio
slug: r-rstudio-no-docker-com-2-cliques
---

Quem já me pagou uma visita aqui já deve ter percebido umas três coisas:

  1. eu gosto muito de usar R;

  2. eu gosto muito de usar o Docker;

  3. eu gosto de combinar os dois;

Como sou colaborador do [r-bloggers](http://www.r-bloggers.com/), talvez o agregador de blogs mais importante para comunidade de usuários de R & Data Science no mundo, eu regularmente publico material em inglês aqui, infelizmente. Infelizmente porque eu percebi que deixei de compartilhar alguns recursos muito úteis que qualquer usuário do R & RStudio poderia se beneficiar, principalmente no Brasil onde eu percebo que falta material sobre a linguagem. Assim vou remediar um pouco esse problema (eu espero!) com uma dica para o uso do Docker, principalmente para professores que usam o R em seus cursos.

## Mas para qual PROBLEMA?

Bem, um dos principais problemas que eu já tive em cursos onde o R é o ambiente principal utilizado é com relação a uniformidade do ambiente de trabalho dos alunos. Acredito que quem já usou o software alguma vez, com grandes turmas, já deve ter se deparado com o seguinte cenário: você escolhe usar o R pois não tem um software estatístico no laboratório, decide usar um RStudio, ou alguma outra interface gráfica para "facilitar" a vida dos alunos, MAS, INFELIZMENTE, a "informática" da instituição nunca consegue garantir um ambiente 100% para os alunos, e você só descobre o problema na hora da aula, justamente quando os alunos precisam rodar aquele script super importante que você preparou em casa! E não adianta reclamar, pois por mais que você solicite  nunca está 100%.

Isso é um grande problema, mas talvez não seja culpa só do departamento de TI da instituição. O grande problema é que é muito difícil garantir que tudo funcione quando os laboratórios das universidades utilizam computadores com diferentes configurações, diferentes sistemas operacionais e tem equipes com diferentes perfis. Enfim, é um problema grave e que não para por ai.

Outro problema associado a esse, e que também traz uma tremenda dor de cabeça, é quando você pede para um usuário instalar o R & RStudio em casa. Em um universo perfeito (no meu!) todo munda usa o Linux e com um [apt-get install](https://help.ubuntu.com/12.04/serverguide/apt-get.html) está tudo resolvido. Infelizmente, a maioria das pessoas não só usa o Windows como também usa o Word como editor de textos principal para preparar trabalhos e relatórios ([LATEX](https://pt.wikipedia.org/wiki/LaTeX) e [Markdown](https://pt.wikipedia.org/wiki/Markdown)?!). Dado isto, aquele relatório bonitinho e bem formatado que você esperava que pudesse ser produzido na aula mesmo, só copiando e colando os códigos em R no [RMarkdown](http://rmarkdown.rstudio.com/) esquece! Não só porque a galera não quer usar, mas porque quando eles tentam compilar para Latex o miserável nunca está instalado e funcionando corretamente!

## Qual seria a solução?

A solução está no Docker. Eu já falei dele aqui nesse blog mas para quem caiu aqui de para-quedas vou fornecer o mesmo texto sobre o Docker.

## Docker

Se você de alguma forma acompanha notícias do mundo open source então você provavelmente já ouviu falar do [Docker](http://www.docker.com/). O Docker é uma ferramenta fantástica que permite criar contêineres de software, totalmente isolados do sistema operacional hospedeiro. Ele funciona como uma máquina virtual, mas é simplesmente muito mais leve.

A ideia por trás do docker é que o desenvolvedor cria um contêiner com todas as bilbiotecas e dependêcias que desejar garantindo que tudo funciona. O responsável pela implantação do software não precisa saber o que há dentro do contêiner, ele só precisa ser capaz de rodar contêineres no servidor. Ainda que uma funcionalidade como essa pudesse ser obtida com máquinas virtuais, elas acabavam vindo com muito mais que o necessário, tal que os arquivos eram muito grandes (>>1Gb) e tornavam o sistema hospedeiro muito lento.

O docker por outro lado, não usa um SO completo, ele compartilha o mesmo Kernel do hospedeiro mas é um ambiente completamente isolado. Assim, rodar contêineres do docker é muito, mas muito mais leve que rodar uma máquina virtual. Os recursos do docker não param por ai, ele também permite uma espécie de versionamento de contêineres e tem uma espécie de github para contêineres, o [Docker Hub](https://hub.docker.com/account/signup/), de onde o usuário pode baixar e usar imagens prontas para diversos softwares, como o MySQL, Postgres, LAMP, WordPress, dentre outros. Nesse vídeo um dos criadores do projeto fala sobre o Docker.

## R & RStudio, Rocker o Kitematic

Logo depois que Docker apareceu, apareceram também diversos projetos associados que criaram um ecossistema de ferramentas. Dentre estas ferramentas, uma que é muito interessante é o [Kitematic](https://kitematic.com/). O Kitematic é uma interface gráfica para manipulação de contêineres e acesso ao Docker Hub. Com ele o usuário praticamente não precisa saber nada sobre docker para poder utilizar um software "conteinerizado". Assim, o usuário pode instalar com alguns cliques, desde o Wordpress, um [App Shiny](http://www.flaviobarros.net/2015/08/10/share-your-shiny-apps-with-docker-and-kitematic/) ou o RStudio.

Especialmente no caso do R & RStudio & Shiny, existe um projeto chamado [Rocker](http://dirk.eddelbuettel.com/blog/2014/10/23/) que mantém contêineres oficiais para o R, o [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) e o [Shiny Server](https://www.rstudio.com/products/shiny/). Com esses contêineres, com apenas alguns comandos do docker, você pode ter um RStudio server, completamente configurado, rodando na sua máquina, sem precisar instalar o R ou qualquer outra biblioteca. Tudo que você precisa é instalar o docker, e se ele estiver instalado COM CERTEZA qualquer pessoa que rodar seu contêiner vai conseguir ter acesso a um ambiente sempre igual. Enfim, é a solução para a homogeneização de ambientes para cursos e universidades, em qualquer sistema operacional.

## Como eu posso fazer isso?

Bom, ao invés de me alongar mais no texto vou te apresentar um screencast de exemplo. Veja que eu estou usando o Linux, onde o Kitematic roda ainda de forma [experimental](https://github.com/kitematic/kitematic/issues/49), mas o mesmo procedimento pode ser feito no Windows e no Mac. O Kitematic suporta oficialmente estas duas plataformas.

https://youtu.be/0iKT-JX-P6Y

## Conclusão

É uma solução que pode universalizar uma plataforma idêntica para todos. Em resumo apresenta as seguintes vantagens em relação a uma instalação tradicional:

  1. Todos rodam uma plataforma idêntica. Se é possível fazer algo em uma então será possível repetir em todas, COM CERTEZA;

  2. Não há problemas com bibliotecas, o que estiver no contêiner todos vão ter;

  3. Só é necessário instalar um único programa, o Docker;

  4. É uma solução multiplataforma: funciona no Linux, no Windows no Mac. Os ambientes são idênticos;

  5. Não há necessidade de usar a linha de comando, só interfaces gráficas;
