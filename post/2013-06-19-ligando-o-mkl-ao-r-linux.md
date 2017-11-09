---
title: Recompilando o R 3.0.1 com suporte à MKL
author: Flavio Barros
date: '2013-06-19'
categories:
  - R e RStudio
tags:
  - Compilação
  - MKL
  - R
slug: ligando-o-mkl-ao-r-linux
---

Antes de você começar a leitura gostaria de destacar que existem outros excelentes posts sobre o assunto:

1. [Compiling 64-bit R 2.10.1 with MKL in Linux](http://www.r-bloggers.com/compiling-64-bit-r-2-10-1-with-mkl-in-linux/)

2. [Speeding up R with Intel's Math Kernel Library (MKL)](http://www.r-bloggers.com/speeding-up-r-with-intels-math-kernel-library-mkl/)

3. [Performance benefits of linking R to multithreaded math libraries](http://blog.revolutionanalytics.com/2010/06/performance-benefits-of-multithreaded-r.html)

4. [Using MKL-Linked R in Eclipse](http://www.r-bloggers.com/using-mkl-linked-r-in-eclipse/)

no entanto, como eu fui tentar instalar a MKL no R 3.01 (Debian Wheezy) e tive alguns contratempos, gostaria de compartilhar essa atualização.

Primeiramente, vamos às siglas: o que é MKL? MKL é uma sigla para Math Kernel Library, isto é, uma biblioteca que oferece uma séries de funções matemáticas como as que são usadas para efetuar um produto matricial ou mesmo uma [transformada de Fourier](http://feeds.feedburner.com/FlavioBarrosR-bloggers). Mas o que ela tem de especial e porque eu precisaria de um biblioteca de funções como essa no R?

![mkl-large-product-plain](http://www.flaviobarros.net/wp-content/uploads/2013/06/mkl-large-product-plain.png)

Primeiramente é importante salientar que independente do objetivo que se tenha ao utilizar a MKL, esta é uma biblioteca específica para processadores da [Intel](http://software.intel.com/en-us/intel-mkl). Se você utiliza outras arquiteturas é provável que a MKL e esse post sejam inúteis para você. MAS, especificamente em relação ao R, você pode já ter ouvido falar que ele pode ser muito [lento](http://www.r-bloggers.com/how-slow-is-r-really/). Uma das origens de um código lento, principalmente nesses processadores novos da Intel como o core i5 ou i7, é que quando você instala o R padrão (os binários disponíveis para download no [CRAN](http://www.r-project.org/)) você está instalando uma séries de bibliotecas numéricas que executam operações matemáticas onde o desempenho é crítico. Essas funções podem ser escritas em C, C++ ou Fortran de forma obter um desempenho superior. NO ENTANTO, as bibliotecas numéricas que vem com o R ([generic BLAS](http://en.wikipedia.org/wiki/Basic_Linear_Algebra_Subprograms); [LAPACK](http://en.wikipedia.org/wiki/LAPACK)) não tem recursos de [multithreading](http://en.wikipedia.org/wiki/Multithreading_(software)#Multithreading). Na prática isso significa que se você têm um processador com 8 cores, ao usar o R padrão você estará realizando várias operações críticas com [um só processador!](http://www.r-bloggers.com/speeding-up-r-with-intels-math-kernel-library-mkl/)

Como resolver o problema? Uma solução possível é a instalação da MKL no R. A MKL, além dos recursos de multithreading, também tem otimizações específicas para processadores da Intel, tendo um desempenho muito superior às bibliotecas tradicionais. Assim, a partir de agora vou mostrar como você pode baixar e instalar essa biblioteca junto ao R no seu computador. Mas antes algumas ressalvas:

1) Se você estiver utilizando o Windows, talvez seja mais interessante para você utilizar o R da [Revolution Analytics](http://www.revolutionanalytics.com/) que já [vem por padrão com a MKL instalada](http://blog.revolutionanalytics.com/2010/06/performance-benefits-of-multithreaded-r.html). O R da Revolution tem um licença gratuita para uso acadêmico.

2) Se você usa o Linux [Red Hat](http://www.redhat.com/) e derivados ([CentOS](http://www.centos.org/)) o R da Revolution também deve ser a melhor opção.

3) Se você usa o Ubuntu você pode instalar o R dos repositórios e depois instalar os pacotes [r-revolution-revobase](http://packages.ubuntu.com/lucid/r-revolution-revobase), [revolution-r](http://packages.ubuntu.com/lucid/revolution-r) e [revolution-mkl](http://packages.ubuntu.com/lucid/revolution-mkl).

e nesse momento você pode estar se perguntando: se existem essas opções porque eu recompilaria o R?? Bom, exitem vários motivos: você pode querer fazer uma instalação customizada; usar o R para fins comerciais sem ter que pagar um licença para a Revolution; aprender como compilar o R. Dito isto, gostaria se salientar também que a [documentação oficial do R](http://cran.r-project.org/doc/manuals/R-admin.html#MKL) é um pouco confusa e desatualizada em relação à instalação da MKL. É por isso que estou escrevendo esse post.

**1. Baixando e Instalando a MKL**

Não vou me alongar nessa parte, mas o processo de instalação é realmente muito simples: basta entrar no site da [MKL](https://registrationcenter.intel.com/RegCenter/NComForm.aspx?ProductID=1461) e se cadastrar. Em seguida você vai receber um e-mail com as instruções para o download. Extraia o arquivo de download e você vai encontrar scripts e pacotes  [RPM](http://en.wikipedia.org/wiki/RPM_Package_Manager). Assim se você estiver tentando instalar no Debian pode ser necessário o suporte ao RPM. Instale utilizando o script install.sh. A partir desse momento vou supor que sua instalação da MKL está em /opt/intel/mkl/11.0.

**2. Recompilando o R**

Para recompilar o R você vai precisar do [código fonte](http://cran.r-project.org/src/base/R-3/R-3.0.1.tar.gz). Extraia para o diretório de sua escolha com:

````r

<blockquote>
  tar -zxvf R-3.0.1.tar.gz
  [/sourcecode]
</blockquote>

Antes de iniciar a compilação é necessário definir valores corretos para duas variáveis do sistema: MKLROOT e LD_LIBRARY_PATH, que pode ser feito de uma vez com o seguinte comando:

````r

<blockquote>
  source /opt/intel/mkl/bin/mklvars.sh intel64
  [/sourcecode]
</blockquote>

observe a localização do arquivo mklvars.sh que pode estar em uma lugar diferente dependendo da sua instalação da MKL.

Definidas as variáveis do sistema basta executar a compilação canônica no Linux, com ./configura && make && make install. O momento de "dizer" ao compilador onde está instalada a MKL e permitir que o R tenha acesso a ela é no ./configure. Assim:

````r

<blockquote>
  ./configure --enable-R-shlib --enable-threads=posix --with-lapack --with-blas="-fopenmp -m64 -I$MKLROOT/include -L$MKLROOT/lib/intel64 -lmkl_gf_lp64 -lmkl_gnu_thread -lmkl_core -lpthread -lm"
  make
  make install
  [/sourcecode]
</blockquote>

E a partir desse momento o seu R já tem suporte às bibliotecas multithreading da Intel. Para garantir que as bibliotecas estão instaladas corretamente rode este [script de benchmark](http://r.research.att.com/benchmarks/R-benchmark-25.R) antes e depois da instalação, e compare as diferenças.

````r
source(R-benchmark-25.R)
````

Caso você já tenha uma instalação do R no seu computador, pode ser necessário reinstalar todos os pacotes novamente. Uma forma fácil de fazer isso é com o seguinte comando:

````r
update.packages(checkBuilt=TRUE)
````

Só como uma referência, no meu notebook (core i7, 8Gb de RAM) um produto de duas matrizes 5000x5000 leva em torno de 19 segundos com a MKL. Antes com a instalação padrão era mais de 1 minuto!
