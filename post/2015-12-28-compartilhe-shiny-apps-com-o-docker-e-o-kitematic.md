---
title: Compartilhe Shiny Apps com o Docker e o Kitematic!
author: Flavio Barros
date: '2015-12-28'
categories:
  - Docker
  - Linux
  - Shiny
slug: compartilhe-shiny-apps-com-o-docker-e-o-kitematic
---

Há algum tempo atrás eu escrevi nesse blog sobre como "dockerizar" uma aplicação Shiny. Se você não sabe o que é o Docker ou o que eu quero dizer com "dockerizar uma aplicação Shiny" eu lhe aconselho dar uma olhada nesse post:

[Dockerizando Shiny Apps](http://www.rmining.net/2015/04/30/dockerizando-shiny-apps/)

Eu usei esta solução para criar uma forma fácil de fazer o deploy (ou implatação) de Apps Shiny em servidores web, uma vez que na prática, se você quiser fazer isso, você somente têm quatro opções:

  1. Compartilhar os arquivos ui.R e server.R com alguém que seja capaz de roda-los no RStudio.

  2. Compartilhar sua aplicação online por meio do [shinyapps.io](http://www.shinyapps.io/).

  3. Compartilhar seu app online no seu próprio servidor (Ex: no AWS).

  4. Dar um git push para seu próprio servidor.

A solução 1 não é viável para usuários não técnicos; a 2 é fácil, mas pode ser cara; a 3 pode ser mais complicada tecnicamente, mas pode ser mais barata; a solução 4 é um compromisso entre a solução 2 e a solução 3, e é a minha predileta.

ENTRETANTO, recentemente eu soube de um projeto da Docker Inc. com muito potencial: o Kitematic! Com ele o usuário tem uma GUI (interface gráfica) onde ele pode baixar e usar imagens docker. Assim o [Docker Hub](https://hub.docker.com/) poderia em tese se tornar algo como um App Store onde o usuário poderia simplesmente baixar e usar apps instalados em contêineres. Veja esse vídeo para você entender melhor:

https://www.youtube.com/watch?v=QEr340gjV1Q

Isso é fantástico, porque uma vez que você consiga dockerizar um App Shiny, não importa quão complexo ele seja, você pode compartilha-lo tranquilamente no Docker Hub. O Kitematic pode ser usado no Windows, no Mac e [experimentalmente no Linux](https://github.com/docker/kitematic/issues/49). Eu fiz um vídeo (no Linux) mostrando como instalar um app Shiny dockerizado, em alguns cliques:

https://youtu.be/7MH_bCjoFT4

Mas lembre-se, antes de você compartilhar seu app você vai precisar dockeriza-lo e subir para o Docker Hub.
