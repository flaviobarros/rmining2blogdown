---
title: Criando repositórios no Github a partir shell
author: Flavio Barros
date: '2015-05-14'
categories:
  - Git &amp; Github
  - Linux
tags:
  - boas práticas
  - controle de versão
  - web
slug: criando-repositorios-no-github-a-partir-shell
---

Esse vai ser um post curto, com um dica rápida. É algo que eu uso e acho bem útil, portanto vou compartilhar. Bom, inicialmente, para quem não sabe, o [Github](https://github.com/) é um Web Hosting para projetos que utilizam o sistema de controle de versões [git](http://git-scm.com/). O Github é excelente e você pode criar projetos open source, ou mesmo pagar pelo serviço para ter repositórios privados. O Github é hoje o padrão em termos de repositório de projetos de código fonte aberto. A maior parte dos grandes projetos open source é desenvolvida por lá, e só para citar alguns dos grandes, estão lá por exemplo: o [Linux](https://github.com/torvalds/linux), o [WordPress](https://github.com/WordPress/WordPress), vários projetos da fundação [Apache](https://github.com/apache),  [.NET](https://github.com/dotnet), [Django](https://github.com/django/django), [Rails](https://github.com/rails/rails), o [Docker](https://github.com/docker/docker) dentre muitos outros.

Eu uso em diversos projetos meus, a maioria de código fonte aberto. MAS, uma coisa que me incomodava, era a necessidade de entrar na interface web, logar e ter que criar lá o novo repositório. Só depois disso que eu poderia voltar para o terminal e adicionar o repositório remoto com um [git remote](http://git-scm.com/book/pt-br/v1/Git-Essencial-Trabalhando-com-Remotos). De fato, o que eu gostaria mesmo era de fazer tudo do terminal: criar um repositório novo com o git, criar um projeto no github, adicionar o remoto e já dar um git push, sem navegador, sem interface web!

Fazer isso é possível, pois o Github conta com uma [API](https://developer.github.com/v3/) que pode ser utilizada por outras aplicações. Aliás, a bem da verdade, o Github tem GUI's completas para o [Windows](https://windows.github.com/) e para o [MAC](https://mac.github.com/), sem contar as [muitas outras](http://git-scm.com/downloads/guis) que existem por ai, mas eu queria simplesmente um comando. Pensando nisso eu fiz o comando eu mesmo com o [Python](https://www.python.org/): o código fonte você encontra [aqui](https://github.com/flaviobarros/create_git_repo).

    #!/usr/bin/env python
    # This Python file uses the following encoding: utf-8

    import os

    usuario = "seu_usuario"

    print 'Defina o título do projeto: '
    projectname = raw_input()

    print 'Defina a descrição do projeto: '
    description = raw_input()

    comando = 'curl -u ' + usuario + ' https://api.github.com/user/repos ' + "'" + '-d' + '{"name":"' + projectname + '","description":"' + description + '"}' + "'"

    print(comando)
    os.system(comando)

Não é ciência de foguete, mas supre a demanda. Basta trocar o "seu_usuario" pelo seu usuário no Github, colocar esse arquivo no PATH do seu Sistema Operacional e o comando ficará disponível na linha de comando. O fluxo de trabalho fica muito simples:

1) crie um repositório do git do zero;

    git init

2) adicione os arquivos;

    git add -A .

3) dê o primeiro commit;

    git commit -am "Primeiro commit"

4) Use o novo comando create_git_repo e crie o repositório no github;

```r
create_git_repo
```

5) Adicione o remoto;

    git remote add origin git@github.com:seu_usuario/seu_projeto.git

6) Envie tudo para o remoto;

    git push origin master

Enfim, você não precisa sair do terminal!

Um detalhe importante é que na primeira pergunta o script lhe pede o título do projeto. Coloque o título da forma como vai querer o "seu_proejto.git", i.e, se quiser com underline já coloque underline, ou qualquer outro caractere.
