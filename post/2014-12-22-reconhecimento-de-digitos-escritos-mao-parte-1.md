---
title: Reconhecimento de dígitos escritos a mão - Parte 1
author: Flavio Barros
date: '2014-12-22'
categories:
  - Aprendizado de Máquina
  - Estatística
  - Mineração de Dados
  - Preparação de Dados
  - R e RStudio
tags:
  - k-nn
  - mnist
slug: reconhecimento-de-digitos-escritos-mao-parte-1
---

A tarefa de reconhecimento de dígitos escritos a mão foi um dos primeiro [grandes sucessos](http://en.wikipedia.org/wiki/Handwriting_recognition) dos métodos de aprendizado de máquina. Hoje em dia, a tarefa pode ser realizada por diversas [bibliotecas especializadas](http://opencv.org/) com altíssima acurácia (> 97% de acertos), tal que muitas vezes, apesar de utilizarmos indiretamente esses recursos em tablets e smartphones, em geral não sabemos exatamente como o método funciona.

![3_032.BMP](http://www.rmining.net/wp-content/uploads/2014/12/3_032.BMP.png)

Pensando nisso, como já trabalhei com esse problema antes, vou demonstrar nesse post como o processo funciona, as técnicas utilizadas e como implementar tudo na linguagem R. Para começar, vamos trabalhar com o problema de reconhecer se o dígito é 0,1,2,3,4,5,6,7,8,ou 9, isto é, um problema de classificação com 10 categorias.

Vou tentar trabalhar aqui implementando toda a modelagem somente com as funções do pacote base e uns poucos pacotes extras com as funções e algoritmos necessários; em um próximo post, posso tentar utilizar outros pacotes para automatizar as diversas etapas da modelagem.

<!-- more -->

### 1. LEITURA

Os dados do problema são imagens do tipo [PGM](http://en.wikipedia.org/wiki/Netpbm_format), com 64 x 64 pixels por imagem, onde cada pixel tem valor 1 ou 0, indicando se o pixel é preto ou branco. Cada imagem tem um nome no formato X_ yyy.BMP.inv.pgm, onde o X representa o dígito desenhado na imagem. Os dados estão divididos em um conjunto de treino e um conjunto de teste e podem ser baixados nos seguintes links: [teste](http://www.rmining.net/wp-content/uploads/2016/03/teste.zip)  e [treino](http://www.rmining.net/wp-content/uploads/2016/03/treino.zip)

Assim a primeira parte do problema é efetuar a leitura dos dados. Para isso me utilizarei do pacote [pixmap](http://cran.r-project.org/web/packages/pixmap/index.html) com o qual é possível ler e manipular imagens PGM. A seguir, o processo de leitura das imagens e a criação de um vetor com as respostas, isto é, com o número do dígito que está escrito na imagem.

```r
## Carregando o pacote pixmap
library(pixmap)

## Definindo o diretório com as imagens de treino
path_treino <- '/sua/pasta/treino/'

## Define como diretório de trabalho
setwd(path_treino)

## Lê os nomes dos arquivos
files <- dir()

## Retira as classes dos nomes dos arquivos
classes <- as.factor(substring(files,first=1,last=1))

## Cria data.frame para armazenar dados de treino
treino <- as.data.frame(matrix(rep(0,length(files)*64*64), nrow=length(files)))

## Efetua leitura dos dados de treino
for (i in 1:length(files)) {

  ## Lendo as imagens
  x <- read.pnm(files[i])

  ## No slot 'grey' está a matriz de pixels que é retirada e vetorizada
  treino[i,] <- as.vector(x@grey, mode='integer')
}

## Define como diretório de trabalho o local das imagens para teste
path_teste <- '/sua/pasta/teste/'

## Diretório de trabalho
setwd(path_teste)

## Lê os nomes dos arquivos
files <- dir()

## Classes
predic <- as.factor(substring(files,first=1,last=1))

## Cria data.frame para armazenar conjunto de teste
teste <- as.data.frame(matrix(rep(0,length(files)*64*64), nrow=length(files)))

## Leitura do conjunto de teste
for (i in 1:length(files)) {
  x <- read.pnm(files[i])
  teste[i,] <- as.vector(x@grey, mode='integer')
}
```

É importante observar que a matriz de pixels fica armazenada no slot @grey, e que após a leitura é transformada em um vetor, tal que o data.frame final fica com 64x64 colunas e 1949 linhas (o total de imagens). O conjunto de  teste tem somente 50 imagens, logo o data.frame vai ficar com 64x64 colunas e somente 50 linhas. Em suma, cada coluna é um píxel e cada linha uma das diferentes imagens.

###  2. MODELAGEM COM k-nn

Nessa etapa será realizado o aprendizado com o algoritmo k-nn (vizinhos mais próximos) sem nenhum tratamento dos dados. O algoritmo funciona atribuindo as classes às imagens, utilizando os valores conhecidos dos vizinhos mais próximos. Assim, digamos que k=3, o algoritmo busca as três imagens mais próximas, verifica qual é a classe majoritária dessas imagens e atribui essa classe à imagem sem label. É importante escolher um k ímpar para não ocorrer empates, por exemplo 2 vizinhos de uma classe e 2 de outra no caso de k=4.

```r
## Carrega pacote class com o k-nn
library(class)

## Utilizando o k-nn para previsão do dígitos nas imagens de teste
predito <- knn(train=treino, test=teste, cl=classes, k=3, prob=T)

## Resultado
result <- data.frame(cbind(predic, predito, acerto = predic==predito))

## Cálculo da taxa de acerto
sum(result$acerto)/nrow(result)

[1] 0.56
```

E com um k=3 obtivemos uma taxa de acerto de somente 56%, muito aquém do que pode ser obtido. Assim, vamos rodar o algoritmo com diversos valores de k e verificar se conseguimos obter um resultado um pouco melhor.

```r
## Data.frame com todos os resultados
resultado <- data.frame(k = rep(0,101), taxa=rep(0.00,101))

for (i in seq(from=1, to=101, by=2)) {

  ## Imprime o valor de k para controle
  print(i)

  ## Obtém os valores preditos para as imagens
  predito <- knn(train=treino, test=teste, cl=classes, k=i, prob=T)

  ## Salva em um data.frame
  result <- data.frame(cbind(predic, predito, acerto = predic==predito))

  ## Calcula a taxa de acerto e armazena no data.frame
  resultado[i,] <- c(i,sum(result$acerto)/nrow(result))
}

## Elimina linhas com 0
resultado <- subset(resultado, subset=resultado$taxa!=0)

## Plota o resultado para todos os k's
plot(resultado$taxa~resultado$k, main='Taxa de Acerto para o k-nn', xlab='Valores de K', ylab='Taxa de acerto')
```

Obtendo o seguinte resultado:

![valores_k](http://www.flaviobarros.net/wp-content/uploads/2014/12/valores_k.png)

Vejam que obtivemos algo em torno de 78% com k=1, mas que ainda é um resultado muito ruim perto do que pode ser alcançado. Também vale notar que aumentar o k não ajuda muito no fim das contas, mas é importante ficar atento pois um k muito pequeno pode levar ao superajustamento ou overfitting.

### CONCLUSÃO PARCIAL

 Ao que tudo indica a classificação de imagens funciona bem utilizando um algoritmo simples, sem nenhum tipo de tratamento. ENTRETANTO, é possível fazer muito melhor. Na Parte 2 vamos automatizar algumas tarefas com pacote [caret](http://caret.r-forge.r-project.org/) e também vamos explorar outros algoritmos melhores como o [SVM](http://en.wikipedia.org/wiki/Support_vector_machine) e o [RandomForest](http://en.wikipedia.org/wiki/Random_forest).
