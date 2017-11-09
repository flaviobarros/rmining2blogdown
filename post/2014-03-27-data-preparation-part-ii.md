---
title: Data Preparation – Part II
author: Flavio Barros
date: '2014-03-27'
categories:
  - English
  - Estatística
  - R e RStudio
  - r-bloggers
slug: data-preparation-part-ii
---

This time i will talk about how to deal with large text files in chuncks with R. Just to provide some real data to work with download [Airlines](http://stat-computing.org/dataexpo/2009/the-data.html) data, relative to 1988; from now on i will work with this file.

![](http://cloudtimes.org/wp-content/uploads/2011/02/big-data.jpg)

To work with this data i will use  [iterators](http://cran.r-project.org/web/packages/iterators/index.html) package. This package allow you pass the file, line by line, or chunck by chunk, without really load all file to memory. As you can feel the idea try this code:

```r
install.packages('iterators')
library(iterators)
con <- bzfile('1988.csv.bz2', 'r')
```

OK, now you have a connection to your file. Let's create a iterator:

```r
it <- ireadLines(con, n=1)
nextElem(it)
nextElem(it)
```

As you can see you are printing line by line. So, you can work with one line, or a chunk of data even with a large file. If you want to read line by line till the end of the file you can use something like this:

```r
tryCatch(expr=nextElem(it), error=function(e) return(FALSE))
```

that returns a FALSE at the end of the file. This a very useful trick in data preparation with large text files.
