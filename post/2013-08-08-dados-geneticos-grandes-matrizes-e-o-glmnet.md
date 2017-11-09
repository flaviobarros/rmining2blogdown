---
title: Dados genéticos, grandes matrizes e o glmnet()
author: Flavio Barros
date: '2013-08-08'
categories:
  - Estatística
  - R e RStudio
tags:
  - glmnet
  - Lasso
slug: dados-geneticos-grandes-matrizes-e-o-glmnet
---

Recentemente, conversando com um colega, tive contato com um problema com o qual eu nunca tinha trabalhado antes: modelagem com dados genéticos. Não tenho nenhum conhecimento especial do assunto, mas dando uma olhada em alguns artigos da área soube que uma das técnicas mais utilizadas para esse tipo de dado era o [lasso](http://en.wikipedia.org/wiki/Least_squares#Lasso_method).

No R, um dos pacotes mais utilizados para  o lasso é o [glmnet](http://cran.r-project.org/web/packages/glmnet/index.html), que diferente da maioria dos outros pacotes como o lm() não aceita como entrada um [data.frame](http://www.r-tutor.com/r-introduction/data-frame). Assim, antes de iniciar a modelagem, é necessário realizar uma etapa de pré-processamento passando os dados para formato matricial. Feito isso é possível passar uma fórmula ou mesmo passar um vetor com a variável resposta, mais uma matriz com os dados das variáveis.

O problema com a abordagem da fórmula é que, em geral, dados de genoma tem muito mais colunas do que observações. Os dados que eu trabalhei nesse caso tinham 40 mil colunas e somente 73 observações. De forma a criar um pequeno conjunto de dados para teste, rode o código a seguir:

<!-- more -->

    [sourcecode language="r"]
    data <- matrix(rep(0,50*49000), nrow=50)
    for(i in 1:50) {
       x = rep(letters[2:8], 7000)
       y = sample(x=1:49000, size=49000)
       data[i,] <- x[y]
    }

data <- as.data.frame(data)
x = c(rep('A', 20), rep('B', 15), rep('C', 15))
y = sample(x=1:50, size=50)
class = x[y]
data <- cbind(data, class)
````

Assim, com esse conjunto de dados vamos tentar ajustar um modelo com o glmnet():

````r
formula <- as.formula(class ~ .)
X = model.matrix(formula, data)
model <- cv.glmnet(X, class, standardize=FALSE, family='multinomial', alpha=1, nfolds=10)
````

E se você não tiver um computador com muito mais memória RAM do que o meu, provavelmente você vai estourar a memória e dar um crash no R. Qual a solução? Minha primeira ideia foi tentar o [sparse.model.matrix()](http://stat.ethz.ch/R-manual/R-devel/library/Matrix/html/sparse.model.matrix.html) que cria uma matriz de modelo esparsa usando a mesma fórmula. Infelizmente também não funcionou, pois mesmo sendo esparsa a matriz de modelo final ainda é muito grande! O interessante é que esse conjunto de dados, na memória, tem somente 24Mb, mas sempre que se usa o model.matrix o resultado é uma matriz com mais de 1Gb.

A solução que eu encontrei foi fazer a matriz na unha pois nesse caso usei a codificação de 1 000...000. Para isso vamos decodificar a matriz com [variáveis dummy](http://www.ats.ucla.edu/stat/mult_pkg/faq/general/dummy.htm), coluna por coluna e armazenar o resultado em uma matriz esparsa. Em seguida vamos utilizar essa matriz como entrada para o modelo e ver se não vai estourar a memória:

````r

## Cria a matriz de entrada usando a primeira coluna

X <- sparse.model.matrix(~data[,1]-1)

## Verifica se a coluna tem mais de um nível (tipo factor naturalmente!)

for (i in 2:ncol(data)) {

## Se tiver mais de um nível aplica codificação dummy

if (nlevels(data[,i])>1) {
coluna <- sparse.model.matrix(~data[,i]-1)
X <- cBind(X, coluna)
}

## Se tiver um número transforma em factor e depois em numérico.

else {
coluna <- as.numeric(as.factor(data[,i]))
X <- cBind(X, coluna)
}
````

OBS: Preste atenção que como estamos utilizando matrizes esparsas o pacote Matrix é necessário. Também observe que as colunas são ligadas usando o cBind() ao invés de cbind().

A matriz gerada dessa forma ficou muito menor: menos de 70Mb quando eu testei. Felizmente o glmnet() tem suporte a matrizes esparsas e é possível rodar o modelo com:

````r
mod.lasso <- cv.glmnet(X, class, standardize=FALSE, family='multinomial', alpha=1, nfolds=10)
````

Assim é possível criar modelos com esse tipo de dado sem estourar a memória e sem precisar utilizar pacotes para conjuntos grandes de dados como o [bigmemory](http://www.bigmemory.org/) ou o [ff](http://www.bigmemory.org/).
