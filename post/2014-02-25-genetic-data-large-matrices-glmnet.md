---
title: Genetic data, large matrices and glmnet()
author: Flavio Barros
date: '2014-02-25'
categories:
  - English
  - Estatística
  - R e RStudio
  - r-bloggers
tags:
  - glmnet
  - Lasso
slug: genetic-data-large-matrices-glmnet
---

Recently talking to a colleague, had contact with a problem that I had never worked with before: modeling with genetic data. I have no special knowledge of the subject, but taking a look at some articles in the area knew that one of the most used techniques for this type of data was the [lasso](http://en.wikipedia.org/wiki/Lasso_(statistics)#Lasso_method).

In R, one of the most used packages for the lasso is [glmnet](http://cran.r-project.org/web/packages/glmnet/index.html), which unlike most other packages like [lm](https://stat.ethz.ch/R-manual/R-devel/library/stats/html/lm.html) accepts as input a data.frame. So, before you start modeling, you must perform a pre-processing step passing the data to matrix format. Done it is possible to pass a [formula](http://stat.ethz.ch/R-manual/R-patched/library/stats/html/formula.html) or even passing an array with the response variable, plus another with data for the variables.

<!-- more -->

The problem with the formula approach is that, in general, genomic data has more columns than observations. The data that I worked in that case had 40,000 columns and only 73 observations. In order to create a small set of test data, run the following code:

```r
data <- matrix(rep(0,50*49000), nrow=50)
for(i in 1:50) {
    x = rep(letters[2:8], 7000)
    y = sample(x=1:49000, size=49000)
    data[i,] <- x[y]
}

data <- as.data.frame(data)
x <- c(rep('A', 20), rep('B', 15), rep('C', 15))
y <- sample(x=1:50, size=50)
class = x[y]
data <- cbind(data, class)
```

So, with this data set we will try to fit a model with glmnet ():

```r
formula <- as.formula(class ~ .)
X <- model.matrix(formula, data)
model <- cv.glmnet(X, class, standardize=FALSE, family='multinomial', alpha=1, nfolds=10)
```

And if you do not have a computer with more RAM than mine, you will probably leak memory and give a crash in R. The solution? My first idea was to try [sparse.model.matrix()](http://stat.ethz.ch/R-manual/R-devel/library/Matrix/html/sparse.model.matrix.html) that creates a sparse matrix model using the same formula. Unfortunately did not work, because even with sparse matrix, the final model is still too big! Interestingly, this dataset occupies only 24MB from RAM, but when you use the model.matrix the result is an array with more than 1Gb.

The solution I found was to build the matrix on hand. To do this we encode the array with dummy variables, column by column, and store the result in a sparse matrix. Then we will use this matrix as input to the model and see if it will not leak memory:

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

NOTE: Pay attention to how we are using a sparse matrix the Matrix package is required. Also note that the columns are connected using cBind () instead of cbind ().

The matrix thus generated was much lower: less than 70 Mb when I tested. Fortunately glmnet () supports a sparse matrix and you can run the model:

```r
mod.lasso <- cv.glmnet(X, class, standardize=FALSE, family='multinomial', alpha=1, nfolds=10)
```

So you can create models with this type of data without blowing the memory and without use R packages for large datasets like [bigmemory](http://www.bigmemory.org/) and [ff](http://cran.r-project.org/web/packages/ff/index.html).
