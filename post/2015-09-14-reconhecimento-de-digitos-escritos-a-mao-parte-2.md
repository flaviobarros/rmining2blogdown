---
title: Reconhecimento de dígitos escritos a mão – PARTE 2
author: Flavio Barros
date: '2015-09-14'
categories:
  - Aprendizado de Máquina
  - Estatística
  - Mineração de Dados
  - Preparação de Dados
  - R e RStudio
slug: reconhecimento-de-digitos-escritos-a-mao-parte-2
---

Na [Parte 1](http://www.flaviobarros.net/2014/12/22/reconhecimento-de-digitos-escritos-mao-parte-1/) desse post (que já publiquei faz um tempão!) eu fiz uma classificação de imagens de dígitos escritos mão usando o [k-nn](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm) (algoritmo dos vizinhos mais próximos) usando as informações das imagens sem nenhum tipo de tratamento, isto é, sem nenhum método de preparação. Como foi mostrado, o [k-nn](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm) só foi capaz de classificar razoavelmente bem com com k = 1 e conseguiu uma acurácia de apenas 78%, algo muito distante do que ainda pode ser conseguido.

Assim, nesta segunda parte, vou explorar outras alternativas além do [k-nn](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm), e mais do que isso, tentar aplicar algum método de preparação para melhorar a performance dos algoritmos. Aqui vou repetir o procedimento de leitura do outro post, para facilitar a reprodução dessa análise.

OBS: Leia a [Parte 1](http://www.flaviobarros.net/2014/12/22/reconhecimento-de-digitos-escritos-mao-parte-1/) para entender melhor o problema!

### 1. LEITURA

Os dados do problema são imagens do tipo [PGM](http://en.wikipedia.org/wiki/Netpbm_format), com 64 x 64 pixels por imagem, onde cada pixel tem valor 1 ou 0, indicando se o pixel é preto ou branco. Cada imagem tem um nome no formato X_ yyy.BMP.inv.pgm, onde o X representa o dígito desenhado na imagem. Os dados estão divididos em um conjunto de treino e um conjunto de teste e podem ser baixados nos seguintes links: [teste](http://www.flaviobarros.net/wp-content/uploads/2014/12/teste.zip)  e [treino](http://www.flaviobarros.net/wp-content/uploads/2014/12/treino.zip)

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

## Aplicação do PCA

O primeiro procedimento que vou utilizar aqui para tentar melhorar a performance da classificação, é usar o método de [Análise de Componente Principais](https://pt.wikipedia.org/wiki/An%C3%A1lise_de_Componentes_Principais), para fazer a rotação do sistema de coordenadas e então ficar somente com uma parte das componentes principais. No fim das contas, o que estaremos fazendo é uma redução do número de atributos pois preservando somente parte das componentes principais eu estarei reduzindo o número de colunas da matriz de dados.

A ideia por trás desse procedimento é que nem todos os atributos (que nesse  caso são pixels) tem a mesma importância para entender o que está escrito na imagem. Pense que pixels que estão próximos da borda podem ter uma importância menor ao passo que pixels mais ao centro devem ser bem mais importantes. Veja que se tivéssemos 10 atributos (ou variáveis), digamos X1 X2 X3 ... X10, então pela rotação do sistema obteríamos PC1 PC2 PC3 ... PC10. A diferença é que as componentes principais, que serão utilizadas no lugar dos atributos originais, são ordenadas de acordo com a variância capturada, isto  é, a PC1 captura maior variância que a PC2 e assim por diante. Dessa forma poderemos escolher as primeiras que capturam a maior variância desejada, reduzindo assim o número de atributos que entram no modelo.

Um detalhe importante da aplicação desse método é que após encontrar as componentes principais no conjunto de treino DEVEMOS APLICAR A MESMA ROTAÇÃO no conjunto de teste. Essa matriz de rotação DEVE SER A MESMA OBTIDA NO CONJUNTO DE TREINO, uma vez que eu não quero usar um método de redução de atributos que tenha utilizado qualquer informação do conjunto de testes. Isso é importante para garantir que a acurácia global inferida será o mais próximo possível do acurácia do modelo em novas imagens.

### PCA com prcomp

Para aplicar o PCA agora vou utilizar a função do pacote base [prcomp](https://stat.ethz.ch/R-manual/R-patched/library/stats/html/prcomp.html). Com ela vamos obter as componentes principais, no caso o mesmo número de colunas do conjunto de dados original, e também o desvio padrão de cada uma delas. Com essas duas informações poderemos avaliar a variância acumulada das componentes principais e escolher somente as primeiras que contemplem, vamos dizer, 90% da variância nesse conjunto de dados.

```r
############ APlicação do PCA ####################################
## Obtendo componentes principais
pc <- prcomp(treino)
treino_x <- pc$x

## Obtendo as variâncias acumuladas
vars = pc$sdev^2

## Calculando a proporção de variância
props = vars/sum(vars)

## Obtendo as variâncias acumulada
varAcum = cumsum(props)

## Obtendo o número de componentes que capturam pelo menos 90% da variância.
which.min(varAcum < 0.9)

## Aplicando a rotação nos dados de teste
test.p <- predict(pc, newdata = teste)
```

Com 341 componentes principais capturamos 90% da variância. Isto é uma redução agressiva no número de atributos, uma vez que tínhamos mais de 1900 pixels. Agora estamos prontos para aplicar o método k-nn novamente, mas desta vez usando as 400 componentes principais como input (valor aproximado). Esperamos uma melhora.

## K-nn com PCA

```r
## Treinando o knn com as 341 variáveis
predito <- knn(train=pc$x[,1:400], test=test.p[,1:400], cl=classes, k=1, prob=T)

result <- data.frame(cbind(predic, predito, acerto = predic==predito))

sum(result$acerto)/nrow(result)
```

Com k = 1 obtivemos uma performance de 84% de acurácia que já é uma melhora. Há que se notar também que não adianta muito colocar mais componentes e que também outros valores de k parecem não trazer grandes melhoras. Talvez seja a hora de partir para outros tipos de modelos a partir daqui. A seguir o gráfico do desempenho do k-nn ao longo dos valores de k com a inclusão de novas componentes.

![valores_dim](http://www.flaviobarros.net/wp-content/uploads/2015/09/valores_dim.png)

## Árvore de decisão com PCA

Parece que não vamos muito mais longe com o k-nn, assim vamos tentar outros métodos como as árvores de decisão. Aqui vou usar a implementação do algoritmo [CART](https://en.wikipedia.org/wiki/Decision_tree_learning) no pacote [rpart](https://cran.r-project.org/web/packages/rpart/index.html) do R. Vou usar como entrada as 400 componentes principais.

```r
## Data frame de treino e teste
treino_arvore = as.data.frame(cbind(treino_pc[,1:544]))
treino_arvore$classes = classes

teste_arvore = as.data.frame(teste_pc[,1:544])

## Teste com árvore de decisão
library(rpart)

## Criando uma árvore
arvore <- rpart(classes ~ ., data = treino_arvore)

## Fazendo a previsão no conjunto de teste
predito <- predict(object = arvore, newdata = teste_arvore, type = 'class')

## Resultado
result <- data.frame(cbind(predic, predito, acerto = predic==predito))

## Cálculo da taxa de acerto
sum(result$acerto)/nrow(result)
```

O resultado foi inferior ao k-nn, com somente 72% de acurácia global.

A seguir vou apresentar ainda mais algumas tentativas com uma regressão linear, regressão logística multinomial e o randomForest.

## Outros modelos com PCA

```r
## Teste com logística multinomial
library(nnet)

## Ajustando um modelo
multinomial <- multinom(classes ~ ., data = treino_arvore, MaxNWts = 5460)

## Fazendo a previsão no conjunto de teste
predito <- predict(object = multinomial, newdata = teste_arvore, type = 'class')

## Resultado
result <- data.frame(cbind(predic, predito, acerto = predic==predito))

## Cálculo da taxa de acerto
sum(result$acerto)/nrow(result)

#################################################################
## Teste com randomForest
library(randomForest)

## Ajustando um modelo
rf <- randomForest(classes ~ ., data = treino_arvore)

## Fazendo a previsão no conjunto de teste
predito <- predict(object = rf, newdata = teste_arvore, type = 'class')

## Resultado
result <- data.frame(cbind(predic, predito, acerto = predic==predito))

## Cálculo da taxa de acerto
sum(result$acerto)/nrow(result)

#################################################################
## Teste com regressão linear
## Data frame de treino e teste
treino_arvore = as.data.frame(cbind(treino_pc[,1:544]))
treino_arvore$classes = classes

teste_arvore = as.data.frame(teste_pc[,1:544])

## Criando uma árvore
reg <- lm(classes ~ ., data = treino_arvore)

## Fazendo a previsão no conjunto de teste
predito <- predict(object = fit, newdata = teste_arvore)
predito <- round(predito, 0)

## Resultado
result <- data.frame(cbind(predic, predito, acerto = predic==predito))

## Cálculo da taxa de acerto
sum(result$acerto)/nrow(result)
```

Com desempenhos inferiores ou iguais ao k-nn.

## Resumos dos resultados

  * k-nn com k = 1: 84%

  * Regressão Linear: 14%

  * Regressão Logística Multinomial: 64%

  * Árvore de Decisão: 34%

  * RandomForest: 72%

## Conclusão

Por enquanto parece que não conseguimos passar do teto de 84% com o k-nn. Isso pode acontecer por diversos motivos, mas alguns possíveis responsáveis por ainda não alcançarmos os melhores resultados são:

  1. No conjunto original teríamos mais de 60 mil imagens para treinar os algoritmos;

  2. O conjunto de teste é muito pequeno logo há muita variabilidade na estimativa de erro;

  3. Usamos parâmetros default em todos os modelos;

No próximo post vamos tentar encontrar os melhores hiperparâmetros para alguns modelos, tentar avaliar melhor o desempenho dos algoritmos, tentar novos algoritmos e automatizar tudo com o [caret](http://caret.r-forge.r-project.org/).

Gostou do artigo? Curta nossa página no Facebook!
