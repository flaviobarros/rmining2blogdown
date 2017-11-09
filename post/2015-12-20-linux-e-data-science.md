---
title: Linux e data science
author: Flavio Barros
date: '2015-12-20'
categories:
  - Linux
slug: linux-e-data-science
---

Até hoje, infelizmente, nunca escrevi sobre o Linux neste blog. Infelizmente pois o Linux foi, e ainda é, um dos meus hobbies prediletos. Eu tive uma das primeiras experiências com o Linux por meio do [Conectiva 4](https://pt.wikipedia.org/wiki/Conectiva#Vers.C3.B5es_instal.C3.A1veis), em 1999, mas acabei na época não o utilizando integralmente, principalmente, por conta dos famigerados [winmodems](https://pt.wikipedia.org/wiki/Winmodem). Também utilizei esporadicamente outra famosa distribuição Live CD brasileira, o [Kurumin Linux](https://pt.wikipedia.org/wiki/Kurumin), mas só passei a utilizar o Linux integralmente a partir de 2004 com Slackware 10.0.

Enfim, desde 2004 eu utilizo o Linux integralmente em minhas máquinas, desktops e servidores, tal que nesses anos todos tive a oportunidade de ver melhor as diferenças entre as plataformas e se vale a pena ou não utilizar o Linux hoje em dia. Assim, nesse post, pretendo apresentar para os leitores o porque eu acredito que ainda vale muito a pena aprender a utilizar o Linux, porque é importante ter esse conhecimento da perspectiva de um cientista de dados e quais as distribuições Linux que indicaria para alguém utilizar. Então vamos a algumas questões importantes!

## 1. O que é o Linux?

O termo Linux é vulgarmente utilizado para se referir a um [sistema operacional ](https://pt.wikipedia.org/wiki/Sistema_operativo)completo. Entretanto, para ser mais preciso, as distribuições Linux que você encontra por aí, como o [Debian](https://www.debian.org/index.pt.html) e o [Fedora](https://getfedora.org/) por exemplo, são na verdade sistemas operacionais completos, com Kernel, compiladores, bibliotecas e etc, que devem ser designados como [GNU Linux](https://pt.wikipedia.org/wiki/Linux) (pelo menos da perspectiva da [FSF](https://pt.wikipedia.org/wiki/Free_Software_Foundation)). O Linux mesmo, na verdade, é o [Kernel](https://en.wikipedia.org/wiki/Kernel_(operating_system)) Linux, uma parte fundamental de qualquer sistema operacional completo, o núcleo do sistema, e que outros sistemas operacionais, como o próprio [Windows](https://pt.wikipedia.org/wiki/Microsoft_Windows) ou [OS X](https://pt.wikipedia.org/wiki/OS_X), também têm. Não vou entrar em detalhes sobre a história do [Kernel Linux](https://www.kernel.org/), mas em linhas gerais é um Kernel que foi desenvolvido como um projeto pessoal de Linus Torvalds, e que revolucionou a [tecnologia da informação](https://pt.wikipedia.org/wiki/Linus_Torvalds).

## 2. Por que eu deveria aprender a usar o Linux?

Da perspectiva de um Cientista de Dados, ou um profissional de TI, ou qualquer pessoa da área técnica, eu considero muito importante aprender, ou ter uma noção básica deste sistema operacional, porque o Linux praticamente move a internet. Para você ter uma ideia da importância desse sistema operacional hoje, ele é utilizado internamente pelas maiores empresas de tecnologia do mundo, como o Google e a Amazon, e é utilizado na infraestrutura de diversas partes da internet, como roteadores, servidores, hosts e etc. Só para não dizer que eu tirei da cartola essa informação, segundo a [W3techs](http://w3techs.com/), sistemas operacionais [Unix](https://pt.wikipedia.org/wiki/Unix) [contemplam dois terços](http://w3techs.com/technologies/overview/operating_system/all) da fatia de mercado de servidores. Desses dois terços, mais da metade são [máquinas Linux](http://w3techs.com/technologies/details/os-unix/all/all). Também vale ressaltar que um dos sistemas operacionais mais bem sucedidos em dispositivos móveis, o [Android](https://pt.wikipedia.org/wiki/Android), é basicamente o Linux. Assim, mesmo que você esteja usando o Windows ou o OS X na sua máquina, na verdade você acaba indiretamente utilizando o Linux, seja no celular, ao acessar um site ou mesmo no roteador da sua casa.

## 3. Ok, mas porque eu deveria tentar usar no desktop?

Bem, da perspectiva de um cientista de dados é importante, pois com a emergência do Big Data, internet das coisas, web inteligente, uma das formas de ofertar os produtos baseados em dados que você pode criar, é disponibiliza-los na internet, utilizando principalmente o Linux. Assim, o conhecimento que você adquire no uso diário da plataforma, pode ser aproveitado depois quando você precisar fazer implantações de software dos seus produtos e análises. Aliás eu já mostrei aqui nesse blog como fazer implantações de [aplicações Shiny](http://www.rmining.net/2015/05/11/git-pushing-shiny-apps-with-docker-dokku/) e do [Wordpress](http://www.rmining.net/2014/12/03/wordpress-do-digital-ocean/) em um servidor privado usando Linux. Esse blog mesmo que você está lendo foi implantado usando o Linux.

## 4. E para um usuário não técnico?

Na minha opinião uma das vantagens do Linux é que ele é, em geral, mais seguro que sistemas Windows. Eu digo em geral porque isso nem sempre é verdade para todos os usuários, e a segurança não é só uma questão dos sistema operacional em si. Não vou me aprofundar no tema segurança, mas dentre as razões de o Linux ser mais seguro, se destacam:

  * Como a fatia de mercado é menor em desktops, praticamente [não existem vírus](http://www.diolinux.com.br/2014/02/por-que-linux-android-nao-pega-virus.html);

  * Você pode obter a maioria dos aplicativos pela própria distribuição, o que é muito mais seguro;

  * A arquitetura do sistema é mais segura;

  * Como é [open source](https://pt.wikipedia.org/wiki/C%C3%B3digo_aberto) as falhas de segurança podem ser encontradas e corrigidas rapidamente;

Existem diversos outros fatores, mas não vou me aprofundar nesse tema aqui. O importante é saber que do ponto de vista de um usuário mediano o Linux costuma ser mais seguro. Naturalmente é importante tomar cuidados mínimos.

Uma outra questão importante é com relação ao desempenho em computadores antigos. Com relação a isso eu gosto de destacar a história da minha Mãe, que usava o Windows 7 e um Duron com 2Gb de RAM. O micro além de estar sempre muito lento, e praticamente inviabilizar certas tarefas, ainda por cima demorava vários minutos para iniciar e desligar. Com o [fim do suporte ao Windows XP](http://windows.microsoft.com/pt-br/windows/end-support-help) (mais leve) ficou praticamente inviável usar o computador com o Windows 7: além de eu ter que dar manutenção sempre que a visitava, a lentidão atrapalhava muito o trabalho e as atividades dela. Para testar se o Linux, em 2015, finalmente poderia ser usado tranquilamente por um usuário leigo, eu fiz uma experiência instalando o [Elementary OS](http://elementaryosbr.org/) na máquina. O resultado foi muito bom, e até hoje, praticamente um ano, ela tem utilizado o computador tranquilamente, mostrando como o Linux está mais maduro para usuários finais no desktop.

Outra vantagem que eu acho interessante é o uso em micro e pequenas empresas. Afinal muitas empresas brasileiras são tão pequenas que o custo de licenças de software podem pesar muito no orçamento e o recurso da pirataria não é uma opção aceitável. Assim, o uso de máquinas Linux, com suítes de escritório open source ([Libre Office](https://www.libreoffice.org/) por ex), podem ser uma opção viável para estar de acordo com a lei e usufruir de uma infraestrutura de software robusta. Uma outra vantagem ainda é usar o Linux como um [mini servidor](http://sms.it-ccs.com/) na empresa, para compartilhar arquivos, internet, cache, segurança e etc.

## 5. Certo, mas onde eu pego? Qual Linux instalar?

Primeiro gostaria de salientar que o Linux é um pouco diferente do Windows com relação ao que você vai instalar. O Linux é o Kernel, mas organizações ou empresas precisam agregar diversos outros softwares para construir um sistema operacional completo que possa ser usado no Desktop. Assim, essas organizações criam as conhecidas distribuições, como o [Ubuntu](http://www.ubuntu.com/) e o [openSUSE](https://www.opensuse.org/), que você deve baixar e instalar. Para ajuda-lo na escolha vou comentar brevemente algo sobre algumas distribuições, minha impressão geral sobre elas, e alguns links úteis para você.

[tabby title = "Slackware"]

![slackware](http://www.rmining.net/wp-content/uploads/2015/12/slackware.png)

É a distribuição Linux mais antiga em atividade. O Slackware é uma distribuição que não conta com ferramentas gráficas para configuração e instalação de programas. Você deve utilizar somente o terminal. A primeira vista pode parecer um absurdo fazer isso hoje em dia, mas a ideia da distribuição é ser muito simples, segura, robusta e fácil de configurar. Minha sugestão é que, se você nunca usou o Linux, comece com outra distribuição mais fácil de usar (GUI e instalação simples) e depois tente o Slackware. Naturalmente se você tiver a disposição e a mente aberta para utiliza-lo é uma excelente distribuição.

Eu tenho um carinho especial pois a utilizei por muitos anos e foi a primeira distribuição Linux que passei a utilizar exclusivamente. Em um próximo post vou falar sobre ela. Confira [esse vídeo](https://www.youtube.com/watch?v=Irsdf-OFo-E) para maiores detalhes sobre a distribuição.

  * Download: <http://www.slackware.com/>

[tabby title = "Debian"]

![debian](http://www.rmining.net/wp-content/uploads/2015/12/debian.png)

O Debian é uma das mais antigas distribuições Linux. É conhecido como um dos mais bem sucedidos projetos open source e é mantido exclusivamente pela comunidade. Dentre as características mais marcantes da distribuição destaca-se que é uma distribuição muito sólida que conta com diversos pacotes de software e é a base de um ecossistema gigantesco de distribuições, incluindo o famoso Ubuntu. O Debian é um sistema operacional completo que pode ser utilizado como servidor e desktop, que pode ser baixado nas versões stable, testing e sid. O stable é super estável mas com software um pouco desatualizado; o testing, base do Ubuntu e diversas outras distribuições, é a distribuição de testes; o sid é experimental e pode ser muito instável. De acordo com a W3techs, é usado por [mais de 32%](http://w3techs.com/technologies/details/os-linux/all/all) do total de servidores que usam Linux!

O Debian é a segunda distribuição que mais usei, e no geral é muito sólida e livre de bugs. Uso sem problemas no Desktop, mas não sugeriria para o usuário iniciante que veio do Windows, devido ao fato de não contar com as facilidades de outras distribuições. Entretanto, no geral, é uma distribuição mais simples e mais fácil que o Slackware. Excelente em servidores. Vejam esse [vídeo](https://www.youtube.com/watch?v=t9lYbrMS770&index=2&list=PLbBPNfi_li9t3kcNOeGIt4Q9qmhRoReN3) que é muito interessante e conta com maiores detalhes.

Download: <https://www.debian.org/index.pt.html>

[tabby title = "Ubuntu"]

![](http://www.rmining.net/wp-content/uploads/2015/12/ublogo2.png)

O Ubuntu é uma distribuição derivada do Debian, com o foco no usuário final. É simples de instalar e usar e conta com ferramentas exclusivas da distribuição. É uma das distribuições Linux mais utilizadas e é fácil encontrar materiais e ajuda online. O ubuntu lança uma versão nova a cada 6 meses aproximadamente, mas também lança versões com suporte de longo prazo, as versões LTS.

É uma das distribuições que eu sugiro a um iniciante no Linux. Além de ser fácil de configurar, o Ubuntu tem uma empresa por trás, a [Canonical](http://www.canonical.com/), tal que se você precisar de suporte pago (um pequeno negócio por exemplo) é simples obter. Outro ponto positivo é que o Ubuntu tem investido bastante no segmento de servidores, tal que o que você aprender no uso diário com o Ubuntu no desktop pode ser transferido facilmente para a administração de servidores. Veja esse [vídeo](https://www.youtube.com/watch?v=mbPFsV3jl4s&list=PLbBPNfi_li9t3kcNOeGIt4Q9qmhRoReN3&index=3) para maiores detalhes.

Download: <http://www.ubuntu.com/download/desktop>

[tabby title = "Mint"]

![mint](http://www.rmining.net/wp-content/uploads/2015/12/mint.jpg)

O Mint é derivado do Ubuntu. Entretanto é totalmente compatível com o mesmo, compartilhando os mesmos repositórios de software. É uma distribuição "mais pronta" out of the box, tal que basta instalar e começar a usar. O Mint inclui codecs e outras alterações no Ubuntu, e acompanha o lançamento das versões LTS. Na minha experiência com o Mint eu diria que é uma distribuição estável, sólida, com excelente suporte da comunidade, softwares atualizados  e pouco trabalho de manutenção. Enfim, é um excelente distribuição para quem está saindo do Windows e é super fácil de usar. É uma das minhas sugestões junto ao Ubuntu.

Download: <http://www.linuxmint.com/>

[tabby title = "openSUSE"]

![OpenSUSE_official-logo-color.svg](http://www.rmining.net/wp-content/uploads/2015/12/OpenSUSE_official-logo-color.svg_.png)

O openSUSE é uma distribuição Linux que foi originariamente derivada do Slackware, mas que com o tempo passou a ser feita do zero. É uma distribuição muito utilizada na Europa e uma das mais utilizadas no mundo. Assim como o Ubuntu, é uma distribuição que conta com uma empresa por trás, a [Novell](https://www.novell.com/home/), tal que é fácil conseguir versões do SUSE para servidores e com suporte. É uma distribuições fácil de usar, com foco no usuário final e eu destacaria que é uma das melhores distribuições com o KDE, conta com uma ferramenta de configuração fantástica, o YAST, e é bastante estável e sólida. Junto ao Ubuntu e ao Mint é uma das minhas sugestões. Assista esse [vídeo](https://www.youtube.com/watch?v=QKC0ALUSBYg&index=5&list=PLbBPNfi_li9t3kcNOeGIt4Q9qmhRoReN3) para maiores detalhes sobre a distribuição.

Download: <https://www.opensuse.org/>

[tabby title = "Fedora/Red Hat/CentOS"]

![fedora_red_hat_centos](http://www.rmining.net/wp-content/uploads/2015/12/fedora_red_hat_centos.jpg)

Começando pelo [Red Hat](https://www.redhat.com/pt-br), esta é uma distribuição que se tornou uma empresa. A empresa Red Hat oferece a versão do sistema operacional voltado para servidores e desktops em empresas, que se caracteriza por ser muito estável e oferecer excelente suporte. A Red Hat é uma das empresas de software livre mais bem sucedidas da história e também é uma distribuição que marcou a história do Linux. Se você pretende seguir carreira com Linux em grandes empresas essa é uma distribuição que você deve aprender. Veja esse [vídeo](https://www.youtube.com/watch?v=zr67VlMqsrk&list=PLbBPNfi_li9t3kcNOeGIt4Q9qmhRoReN3&index=4) para maiores detalhes.

O [CentOS](https://www.centos.org/) é a versão gratuita e sem suporte do Red Hat Enterprise. Como a Red Hat utiliza código open source ela deve fornecer o código fonte da distribuição de alguma maneira ([GPL](https://pt.wikipedia.org/wiki/GNU_General_Public_License)). Assim, a equipe do CentOS recompila os fontes, retira as marcas da Red Hat e oferece uma versão comunitária que pode ser usada por empresas e indivíduos, geralmente em servidores. De acordo com a W3techs, os servidores CentOS correspondem a [20%](http://w3techs.com/technologies/details/os-linux/all/all) do total de servidores Linux!

Quando o Red Hat passou a focar a distribuição em grandes empresas nasceu o projeto [Fedora](https://getfedora.org/), que é mantido pela comunidade, com suporte da empresa, e você pode utilizar como desktop. É uma das melhores experiências com o [Gnome](https://www.gnome.org/) que você pode encontrar por aí e funciona como uma espécie de versão de testes para o Red Hat Enterprise. Assim espere várias atualizações, um ciclo curto de lançamentos de versões e novas tecnologias. Eu sugiro que você evite o Fedora no começo; se o Red Hat está para o Debian stable, o Fedora está para o Debian testing. Assim, como uma primeira experiência, minha sugestão é que você tente utilizar distribuições com suporte de mais longo prazo, e menos experimentais. ENTRETANTO, gostaria de salientar que na última versão, o Fedora 23, o feedback que tenho visto na comunidade é de que a distribuição está bem estável.

Download: <https://getfedora.org/>

[tabbyending]

## 6. O que você usa e por que?

Eu usei por muitos anos o Slackware em desktops e servidores. Depois com o tempo eu testei diversas outras distribuições por algumas semanas (Fedora, Arch Linux, Mandriva, Ubuntu, SUSE e etc), mas acabei ficando no Debian stable. Cada usuário tem suas necessidades, e de acordo com o meu perfil, minhas demandas são as seguintes:

  * Distribuição **estável e livre de bugs** para trabalhar. Para mim é fundamental, uma vez que quando eu tenho que finalizar algo o computador não pode ficar no meu caminho.

  * Eu uso **100% a linha de comando** para tarefas administrativas. Assim, para meu uso, uma interface carregada, cheia de recursos, não é necessária.

  * **Mínimo possível de atualizações** e não necessito usar versões novas de vários programas.

  * **Raramente jogo no computador**, assim eu não preciso de um sistema com tanto suporte a jogos.

  * Passo quase 100% do tempo no **RStudio, Navegador e no terminal**. As vezes assisto a algum vídeo.

  * Faço algumas **edições de vídeo simples**.

Devido as minhas necessidades o Slackware e o Debian stable são mais do que suficientes. São minhas distribuições prediletas por isso. Mas veja que meu perfil é bastante diferente da maioria dos usuários, pois muitas pessoas jogam no computador, preferem interfaces gráficas ao invés de usar a linha de comando e preferem ter versões mais novas dos programas. Nesses casos, em geral, o Slackware e o Debian não são as melhores opções. E também é importante lembrar que não existe melhor ou pior, cada distribuição atende melhor determinados perfis de usuário. Todas as distribuições que eu usei foram excelentes, mas as que eu tive melhor experiência em termos das minhas demandas foram essas duas. Também gostaria de salientar que não existe nenhum mérito em utilizar a linha de comando ao invés de interfaces gráficas. Eu acostumei a fazer isso por uma questão de comodidade.

## Conclusão

O fato concreto é que existem várias distribuições Linux que são tão fáceis de usar quanto o Windows ou o OS X. Essas distribuições são bonitas, seguras, fáceis de usar e instalar, e você ainda vai aprender algo que depois pode usar para fazer implantação em servidores. As três distribuições que eu recomendo são o [Mint](http://www.linuxmint.com/download.php), [Ubuntu LTS](http://www.ubuntu.com/download/desktop) e o [openSUSE](https://software.opensuse.org/421/pt_BR). Veja que eu estou deixando de fora o Fedora, pois na minha experiência com o mesmo, eu percebi que ele tem um volume muito grande de atualizações e costuma experimentar bastante com a distribuição. Fora que lança mais de uma versão por ano com suporte curto. Também deixei de fora distribuições [rolling release](https://pt.wikipedia.org/wiki/Rolling_release), como o [Arch Linux](https://www.archlinux-br.org/), que apesar de excelentes, tem alguns inconvenientes com relação a configuração e atualizações constantes que eu sugeriria para você experimentar depois.

## SITES

Se você quiser aprender mais sobre o Linux existem diversos sites em português com muito material. Vou apresentar uma lista de recomendações aqui:

  * [Viva o Linux](https://www.vivaolinux.com.br/): muitos tutoriais, dicas e etc. Produzido pela comunidade.

  * [Br-Linux](http://br-linux.org/): notícias sempre atualizadas sobre o Linux.

  * Blog [Diolinux](http://www.diolinux.com.br/): dicas linux, reviews e vídeos.

  * Blog [Linux Descomplicado](http://www.linuxdescomplicado.com.br/): notícias e reviews.

  * Blog [Toca do Tux](http://tocadotux.blogspot.ca/): notícias, vídeos e reviews. Vários vídeos que eu postei aqui são de lá!

  * [O Manual do Administrador Debian](https://debian-handbook.info/browse/pt-BR/stable/): um livro online fantástico. Tem tudo que você precisar saber sobre o Debian e a administração do Debian.

  * [Slackware Essentials](http://slackbookptbr.sourceforge.net/slackbookptbr-1.0.2.pdf) (Português): é o guia essencial do Slackware. Não é tão completo quanto o manual do Administrador Debian, mas é muito bom também.
