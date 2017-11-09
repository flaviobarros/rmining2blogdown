---
title: Git push no Django com o Dokku - básico
author: Flavio Barros
date: '2015-05-02'
categories:
  - Digital Ocean
  - Django
  - Docker
  - Linux
tags:
  - git
  - implantação
  - web
slug: git-push-no-django-com-o-dokku-basico
---

## 1. Introdução

Quem trabalha com web, se não conhece, pelo menos já ouviu falar do [Heroku](https://www.heroku.com/). O Heroku é um PaaS, uma sigla para (Platform as a Service) ou Plataforma como Serviço. A ideia por detrás do Heroku é que o desenvolvedor não precise se preocupar mais com os problemas relacionados à implantação do seu software, ele simplesmente desenvolve, adiciona alguns arquivos no projeto, dá um [git push](http://git-scm.com/book/pt-br/v1/Git-Essencial-Trabalhando-com-Remotos) para o Heroku e ele coloca no ar para você.

Isso é praticamente o sonho para quem trabalha com web, principalmente por conta da dor de cabeça que é configurar e manter um servidor. Outra vantagem é que você usa um sistema de controle de versões para o seu código (o [git](http://git-scm.com/book/pt-br/v1/Primeiros-passos-Uma-Breve-Hist%C3%B3ria-do-Git) no caso) e você tem muito mais controle sobre seu projeto. Por fim, se o tráfego crescer, é simples escalar com Heroku, basta ir adicionando os [dynos](https://devcenter.heroku.com/articles/dynos). Parece ótimo, MAS tem alguma desvantagem nisso? SIM, o [PREÇO](https://www.heroku.com/pricing)!

Só para você entender do que eu estou falando, eu vou fazer o deploy de uma aplicação [Django](https://www.djangoproject.com/). É um projeto que eu fiz seguindo o tutorial do [djangogirls](http://tutorial.djangogirls.org/en/index.html); o código fonte está disponível [aqui](https://github.com/flaviobarros/djangogirls) no Github caso queira reproduzir.

Você pode visitar a aplicação nesse domínio: <http://flavio-django-blog.herokuapp.com/>

## 2. Docker e Dokku

Se você de alguma forma acompanha notícias do mundo open source então você provavelmente já ouviu falar do [Docker](http://www.docker.com/). O Docker é uma ferramenta fantástica que permite criar contêineres de software, totalmente isolados do sistema operacional hospedeiro. Ele funciona como uma máquina virtual, mas é simplesmente muito mais leve. A ideia por trás do docker é que o desenvolvedor cria um contêiner com todas as bilbiotecas e dependêcias que desejar garantindo que tudo funciona. O responsável pela implantação do software não precisa saber o que há dentro do contêiner, ele só precisa ser capaz de rodar contêineres no servidor.

Ainda que uma funcionalidade como essa pudesse ser obtida com máquinas virtuais, elas acabavam vindo com muito mais que o necessário, tal que os arquivos eram muito grandes (>>1Gb) e tornavam o sistema hospedeiro muito lento. O docker por outro lado, não usa um SO completo, ele compartilha o mesmo Kernel do hospedeiro (sim, atualmente ele precisa rodar no Linux) mas é um ambiente completamente isolado. Assim, rodar contêineres do docker é muito, mas muito mais leve que rodar uma máquina virtual. Os recursos do docker não param por ai, ele também permite uma espécie de versionamento de contêineres e tem uma espécie de github para contêineres, o [Docker Hub](https://hub.docker.com/account/signup/), de onde o usuário pode baixar e usar imagens prontas para diversos softwares, como o MySQL, Postgres, LAMP, WordPress, dentre outros.

O [Dokku](http://progrium.com/blog/2013/06/19/dokku-the-smallest-paas-implementation-youve-ever-seen/) por sua vez, foi o "killer app" do Docker. Foi com esse projeto que muitas pessoas conseguiram ver o tamanho do potencial dessa ferramenta. De fato, como vamos ver aqui, com ele é possível ter praticamente a mesma funcionalidade do Heroku, só que a um custo muito mais baixo, uma vez que o [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6), oferece um VPS básico (Virtual Private Server, ou Servidor Privado Virtual) por U$5,00 ou R$15,00 aproximadamente. E não é um hardware qualquer, mas com 512Mb de RAM e 20Gb em SSD! Isso é mais do que suficiente para rodar diversas instalações do WordPress, não somente uma.

## 3. Instalando o Dokku no Digital Ocean

Inicialmente, já vou alerta-lo que, ao invés do [dokku](https://github.com/progrium/dokku) original, eu vou instalar e usar um fork, o [dokku-alt](https://github.com/dokku-alt/dokku-alt). Mas por quê? Bem, simplesmente porque o dokku-alt já vem com diversos recursos e [plugins](http://progrium.viewdocs.io/dokku/plugins) instalados que eu precisaria instalar na unha caso quisesse o dokku original. Para facilitar para o leitor, ao invés dos comandos, no screencast abaixo eu mostro a instalação do dokku em um droplet do [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6).

https://youtu.be/PwvEOdXR3q4

A etapa final da instalação exige que você tenha uma chave SSH pública. Caso você não tenha, siga [esse tutorial](http://Atualmente eu já uso o dokku para hospedar esta instalação do WordPress. Assim, eu vou exp). Veja o comando e um exemplo do que você vai copiar e colar:

    $ cat ~/.ssh/id_rsa.pub

e a saída vai ser algo assim:

    ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAklOUpkDHrfHY17SbrmTIpNLTGK9Tjom/BWDSU
    GPl+nafzlHDTYW7hdI4yZ5ew18JH4JW9jbhUFrviQzM7xlELEVf4h9lFX5QVkbPppSwg0cda3
    Pbv7kOdJ/MTyBlWXFCR+HAo3FXRitBqxiX1nKhXpHAZsMciLq8V6RjsNAQwdsdMFvSlVK/7XA
    t3FaoJoAsncM1Q9x5+3V0Ww68/eIFmb1zuUFljQJKprrX88XypNDvjYNby6vw/Pb0rwert/En
    mZ+AW4OZPnTPI89ZPmVMLuayrD2cE86Z/il8b+gw3r3+1nKatmIkjn2so1d01QraTlMqVSsbx
    NrRFi9wrf+M7Q== schacon@agadorlaptop.local

que você deve copiar e colar na janela do navegador, no último passo da instalação do dokku-alt.

## 4. Git push com Django

Atualmente eu já uso o dokku para hospedar esta instalação do WordPress. Assim, eu vou experimentar o mesmo processo utilizado anteriormente no Heroku, só que no meu próprio PaaS com o dokku-alt, que já está configurado no domínio desse site.

Agora você pode visitar a aplicação no domínio: <http://djangogirls.flaviobarros.net/>

Veja que o procedimento foi idêntico ao Heroku e dessa forma é possível replicar a mesma funcionalidade por um custo muito menor.

## 5. Detalhes importantes

Você deve gerar um Procfile dentro do seu projeto. No caso desse repositório, o arquivo já existia uma vez que foi preparado para isso, mas caso estiver criando um projeto do zero, você deve adicionar esse arquivo. O mesmo vale para o requirements.txt. Para todos os efeitos, os seguinte comandos geram estes arquivos:

    pip freeze > requirements.txt

    echo "web: gunicorn djangogirls.wsgi" > Procfile

Outro detalhe importante é que eu estou usando o sqlite3 como banco de dados. Assim, eu gerei o arquivo do banco localmente, o db.sqlite3, tal que este arquivo foi junto no git push. EM GERAL VOCÊ NÃO VAI FAZER ISSO. O que você devia fazer é uma configuração alternativa local, com o SQlite3, e uma configuração em produção, utilizando o Postgres por exemplo.

No caso do Postgres ou do MySQL, o dokku-alt já conta com plugins para a criação desses bancos e comandos para linkar o banco a sua aplicação. Em outro post posso mostrar mais detalhes.

##  Conclusão

Se você já usa o Heroku, passar a utilizar o Dokku é simples. Basta preparar um servidor e você pode trabalhar normalmente, como se estivesse usando o Heroku. Caso você nunca tenha utilizado o Heroku minha sugestão é que você teste as duas formas de instalação.

IMPORTANTE: acessando qualquer link dessa página para o [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6), ao fazer o cadastro você vai ganhar U$10,00 de crédito sem compromisso de manter o serviço. Com esse crédito você poderá manter um VPS simples, com 512Mb de RAM, por dois meses de graça! Use os links desse post.
