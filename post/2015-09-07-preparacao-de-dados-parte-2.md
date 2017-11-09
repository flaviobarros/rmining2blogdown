---
title: Preparação de dados - Parte 2
author: Flavio Barros
date: '2015-09-07'
categories:
  - Estatística
  - Mineração de Dados
  - Preparação de Dados
  - R e RStudio
slug: preparacao-de-dados-parte-2
---

Neste post eu vou falar sobre como trabalhar com GRANDES ARQUIVOS DE TEXTO em [chunks](http://www.wordreference.com/enpt/chunk) no R. Esse pode ser um problema complicado e que pode aparecer na vida do analista trabalhando com [arquivos de log](https://en.wikipedia.org/wiki/Logfile) por exemplo. Antes de continuar o post gostaria de salientar que estou utilizando o termo _chunk _para designar um pedaço do arquivo de texto, isto é, estou dizendo que vamos trabalhar com grandes arquivos de texto, pedaço por pedaço.

## Mas por que chunks?

Bom, se você já teve oportunidade de trabalhar com grandes arquivos de dados no R você já deve ter percebido que dependendo do tamanho do arquivo o simples procedimento de leitura pode demorar muito tempo. Isso ocorre porque o R trabalha com arquivos de dados diretamente na memória RAM tal que arquivos gigantescos ou podem ocupar toda a memória RAM do seu computador, inviabilizando a leitura, ou mesmo podem até ser lidos mas deixarem o computador extremamente lento tomando muito tempo da análise.

Para mostrar um exemplo prático de como trabalhar com este tipo de arquivo no R vamos utilizar um conjunto de dados muito interessante com informações de vôos, o [Airlines](http://stat-computing.org/dataexpo/2009/the-data.html). Vamos trabalhar somente com as informações relativas a 1988, mas que sozinhas já são suficiente para dar uma tremenda dor de cabeça. Para trabalhar com esses dados eu vou usar o pacote [iterators](https://cran.r-project.org/web/packages/iterators/index.html), um pacote que permite ler um arquivo linha por linha, ou chunk por chunk, mas sem ter que carregar o arquivo inteiro na memória. Para você ter uma ideia de como isso funciona tente rodar esse código:

```r
## Instalando e carregando o pacote
install.packages('iterators')
library(iterators)

## Efetuando a conexão de leitura do arquivo, direto do formato bz2 (uma espécie de zip).
con <- bzfile('1988.csv.bz2', 'r')
```

Preste atenção que o objeto **con** armazena somente uma CONEXÃO. EU não estou lendo o arquivo ainda e portanto não o estou carregando na memória. Esse tipo de conexão é parecida com a que você poderia criar em um [sistema de gerenciamento de banco de dados](https://pt.wikipedia.org/wiki/Sistema_de_gerenciamento_de_banco_de_dados) antes de fazer a primeira consulta em SQL.

OK, agora que temos a conexão vamos criar um iterator (ou iterador):

```r
## Cria um iterador que lê uma linha do arquivo por vez (n = 1)
it <- ireadLines(con, n=1)

## Comando para ir retornando linha a linha
nextElem(it)
nextElem(it)
```

Como você pode ver você está imprimindo no terminal linha por linha do arquivo. Assim, se você quiser trabalhar linha por linha, ou mesmo chunk por chunk (n > 1) você pode ir carregando pequenos pedaços do arquivo que não vão ocupar a memória inteira.

## Mas como saber qual é a última linha?

Como estamos lendo linha a linha não temos como saber de antemão quantas linhas devemos ler. Assim a solução para o problema é tentar pegar o erro que ocorre quando chegamos na última linha do arquivo e pedimos mais uma com o nextElem(it). Quando fazemos isso a função retorna um erro, o qual podemos "transformar" em um FALSE do R e usar esse sinal em um programa para encerrar o trabalho.

```r
tryCatch(expr=nextElem(it), error=function(e) return(FALSE))
```

esse comando retorna um  FALSE quando chega no fim do arquivo. Esse é um truque muito útil na preparação com grandes arquivos de texto.

## Exemplo do Stackoverflow

Há um tempo atrás surgiu uma pergunta no stackoverflow justamente sobre grandes arquivos de texto. Você encontrar a pergunta aqui: [Pre-processar grande arquivo de texto](http://pt.stackoverflow.com/questions/35469/pre-processar-grande-arquivo-de-texto-txt-substituir-por/35645#35645). Veja que há também uma solução interessante do Carlos Cinelli (do [analisereal.com](http://analisereal.com/)) que não faz uso do iterators. É uma solução que usa o seguinte método: lê 4000 linhas por vez e pula as linhas já lidas anteriormente. Eu acredito que a estratégia com iterators pode ser melhor pois você só vai varrer o arquivo uma vez, isto é, na solução com iterator você tem um apontador para a linha que parou e pode continuar a partir dali, até chegar no final do arquivo. Na solução usando o read.csv() ou read.table() em cada iteração o arquivo é lido novamente e todas as linhas já lidas são puladas.

```r
## Carregando o pacote
library(iterators)

## Defindo a função que troca vírgula por ponto
change_dot <- function(file, saida='teste.txt', chunk=1) {

  ## Cria conexão com arquivo a ser lido
  con1 <- file(file, 'r')

  ## Cria conexão onde será escrito o novo arquivo
  con2 <- file(saida, open = 'w')
  linha <- 0

  ## Criando o iterador
  it <- ireadLines(con1, n=chunk)

  ## Lendo a primeira linha e escrevendo no outro arquivo
  out <- tryCatch(expr=write(x = gsub(pattern = ',', replacement = '.', x = nextElem(it)), con2),
                   error=function(e) e)

  ## Observe o uso do tryCatch() para saber o fim do arquivo
  while(!any(class(out) == "error")) {
    linha = linha + 1
    print(paste('Escrita linha ', linha))
    out <- tryCatch(expr=write(x = gsub(pattern = ',', replacement = '.', x = nextElem(it)), con2, append = T),
                  error=function(e) e)
  }
}
```

Nesse caso em específico o tempo total para a realização da tarefa foi de aproximadamente 6 segundos.

Gostou do artigo ou achou a dica útil?! Curta nossa página no Facebook!
