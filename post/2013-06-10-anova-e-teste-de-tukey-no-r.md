---
title: ANOVA e  teste de Tukey no R
author: Flavio Barros
date: '2013-06-10'
categories:
  - Estatística
  - R e RStudio
tags:
  - ANOVA
  - Comparações múltiplas
  - post-hoc
  - Tukey
slug: anova-e-teste-de-tukey-no-r
---

Em muitos tipos diferentes de experimentos, com um ou mais de um fator, um dos procedimentos estatísticos mais utilizados é a análise de variância, ou simplesmente [ANOVA](http://www.portalaction.com.br/content/anova). O ANOVA mais simples pode ser chamado "one way" ou mesmo "single-classification" e envolve a análise de dados amostrados de mais de uma população ou dados de experimentos com mais do que dois tratamentos.

Nesse post não é o meu objetivo estudar a fundo o ANOVA, mas sim mostrar como aplicar o procedimento no R e fazer uma análise "post-hoc" chamada [teste de Tukey](http://www.portalaction.com.br/content/31-teste-de-tukey). Quando estamos realizando uma análise de variância, a hipótese nula considerada é que não há diferença na média dos tratamentos, assim, uma vez rejeitada a hipótese nula, a questão é saber quais tratamentos se diferenciam.

Para exemplificar o procedimento vamos analisar uma situação experimental onde uma empresa está aplicando um teste sensorial para um conjunto de 15 provadores em três marcas diferentes de chocolate. Três marcas são comparadas, sendo uma delas a referência, e o objetivo é verificar se existe diferença das marcas com o controle. Nesse experimento temos dois fatores, a marca e os provadores,  e esperamos que os provadores não tenham um efeito significativo. Em cada avaliação o provador tem que determinar essa diferença em uma escala que vai de 0 a 7.

```r
chocolate = data.frame(
Sabor =
c(5, 7, 3,
4, 2, 6,
5, 3, 6,
5, 6, 0,
7, 4, 0,
7, 7, 0,
6, 6, 0,
4, 6, 1,
6, 4, 0,
7, 7, 0,
2, 4, 0,
5, 7, 4,
7, 5, 0,
4, 5, 0,
6, 6, 3
),
Tipo = factor(rep(c("A", "B", "C"), 15)),
Provador = factor(rep(1:15, rep(3, 15))))
```

A média das notas para os tipos A, B e C, foi respectivamente 5,33 5,27 e 1,53. Claramente a marca C, o controle, não foi diferente dela mesma para a maioria dos provadores, como esperado. Uma forma de obter essas médias facilmente por grupo é utilizando o [**tapply**](http://stat.ethz.ch/R-manual/R-devel/library/base/html/tapply.html).

```r
tapply(chocolate$Sabor, chocolate$Tipo, mean)
```

Por fim vamos rodar o ANOVA e avaliar se há diferença entre marcas e provadores.

```r
ajuste = lm(data = chocolate, Sabor ~ .)
summary(ajuste)
anova(ajuste)
```

o que resulta:

    Analysis of Variance Table

    Response: Sabor
              Df  Sum Sq Mean Sq F value    Pr(>F)
    Tipo       2 141.911  70.956   19.21 5.598e-06 ***
    Provador  14  32.578   2.327    0.63    0.8175
    Residuals 28 103.422   3.694
    ---
    Signif. codes:
    0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Pela saída do programa vemos que o p-valor para provadores é 0.8175 indicando que os provadores não tem efeito significativo na resposta. Isso é desejável, uma vez que se espera que os provadores sejam capazes de discernir corretamente as marcas de chocolate. Também na tabela ANOVA vemos que o p-valor para o tipo de chocolate é altamente signigicativo, indicando a diferença entre as marcas. Assim, a partir de agora podemos fazer o teste de Tukey para verificar onde estão as diferenças.

```r
a1 <- aov(data = chocolate, Sabor ~ Tipo + Provador)
posthoc <- TukeyHSD(x=a1, 'Tipo', conf.level=0.95)
```

que resulta:

     Tukey multiple comparisons of means
        95% family-wise confidence level

    Fit: aov(formula = Sabor ~ Tipo + Provador, data = chocolate)

    $Tipo
               diff       lwr       upr     p adj
    B-A -0.06666667 -1.803101  1.669768 0.9950379
    C-A -3.80000000 -5.536435 -2.063565 0.0000260
    C-B -3.73333333 -5.469768 -1.996899 0.0000337

Essa saída indica que as diferenças C-A e C-B são significativas, ao passo que B-A não é significativa. Uma forma mais "fácil" de interpretar essa saída é visualizando os intervalos de confiança para as diferenças das médias.

```r
plot(posthoc)
```

![Rplot](http://www.flaviobarros.net/wp-content/uploads/2013/06/Rplot.jpeg)

Pode-se ver que somente o intervalo de confiança para B-A contêm o 0. Assim, verifica-se que as marcas A e B não se diferenciam entre si, mas se diferenciam da marca controle.

Por fim uma forma alternativa para avaliar as diferenças com uma saída mais parecida com a do [SAS](http://www.sas.com/) é utilizando o pacote [**agricolae**](http://cran.r-project.org/web/packages/agricolae/index.html) do R. Com ele vamos realizar o mesmo procedimento anterior, mas a saída é um pouco diferente.

    library(agricolae)
    HSD.test(ajuste, 'Tipo', console = T)

    Study:

    HSD Test for chocolate$Sabor

    Mean Square Error:  3.693651

    chocolate$Tipo,  means

      chocolate.Sabor   std.err  r Min. Max.
    A        5.333333 0.3737413 15    2    7
    B        5.266667 0.4078593 15    2    7
    C        1.533333 0.5844547 15    0    6

    alpha: 0.05 ; Df Error: 28
    Critical Value of Studentized Range: 3.49926

    Honestly Significant Difference: 1.736435

    Means with the same letter are not significantly different.

    Groups, Treatments and means
    a    A   5.333
    a    B   5.267
    b    C   1.533

onde a saída final indica que tanto A quanto B pertencem ao mesmo grupo, o 'a', e C é diferente dos outros dois, pertence ao grupo 'b'. Espero que esse exemplo permita que outros usuários utilizem o R para fazer esse tipo de análise.
