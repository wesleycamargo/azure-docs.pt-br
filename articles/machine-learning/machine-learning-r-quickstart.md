<properties title="Quick start guide to R for Azure Machine Learning Studio" pageTitle="Guia de início rápido de R para o Studio de Aprendizado de Máquina do Azure" description="Como usar o R no Estúdio de Aprendizagem de Máquina do Azure" metaKeywords="R azure, r machine learning, azure r machin elearning" services="machine-learning" solutions="" documentationCenter="" authors="larryfr" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="larryfr" />

#Guia de início rápido de R para a AM do Azure

Stephen F Elston, PhD.

##  Introdução

A AM do Azure contém muitos módulos poderosos de aprendizagens de máquina e de manipulação de dados.  A poderosa linguagem de R tem sido descrita como a língua franca da análise.  Felizmente, a análise e manipulação de dados na AM Azure podem ser estendidas com R. Essa combinação fornece a facilidade de escalabilidade de implantação da AM do Azure com a flexibilidade e análise profunda de R.

Este documento lhe ajudará a começar rapidamente a estender a AM do Azure usando a linguagem de R. Este guia contém as informações que você precisa para criar, testar e executar um código de R na AM do Azure. Na medida em que você prossegue neste guia de início rápido, você criará uma solução completa de previsão usando a linguagem de R na AM do Azure.  

###Previsão e conjunto de dados

A previsão é um método analítico amplamente empregado e bastante útil. O uso típico varia de previsão de vendas de itens sazonais, determinar níveis de estoque ideal a prever variáveis macroeconômicas. A previsão normalmente é feita com modelos de série de tempo.

Dados de série de tempo são dados no qual os valores têm um índice de tempo. O índice de tempo pode ser regular, por exemplo, a cada mês ou a cada minuto, ou irregulares. Um modelo de série de tempo se baseia em dados de série de tempo. R contém uma estrutura flexível e análise abrangente para dados de série de tempo.

Neste guia de início rápido, trabalharemos com a produção de derivados de leite e dados de preços na Califórnia  Esses dados incluem informações mensais sobre a produção de vários produtos derivados do leite e o preço da gordura do leite, uma mercadoria referência.

Os dados usados neste artigo, juntamente com os scripts de R, podem ser [baixados aqui][download]. Esses dados foram originalmente sintetizados a partir das informações disponibilizadas pela Universidade de Wisconsin em http://future.aae.wisc.edu/tab/production.html.

###	Organização

Passaresmos por várias etapas enquanto você aprende a criar, testar e executar o código R de manipulação de dados e análises no ambiente de AM do Azure.  

* Primeiro, veremos as noções básicas sobre o uso da linguagem de R no ambiente do Studio de AM do Azure.

* Em seguida, discutiremos os diversos aspectos de entrada/saída de dados, código R e gráficos no ambiente de AM do Azure.

* Então, construiremos a primeira parte de uma solução de previsão criando o código de limpeza de dados e transformação.

* Com nossos dados preparados, executaremos uma análise das correlações entre diversas variáveis do nosso conjunto de dados.

* Por fim, criaremos um modelo de previsão de série de tempos sazonais da produção de leite.

##Sumário

* [Interagindo com o R no Studio de AM](#mlstudio)

* [Obtendo dados e de entrada e saída do módulo Executar script R](#scriptmodule)

* [Filtragem de dados e a transformação](#filtering)

* [Objetos de série de tempo e análise de correlação](#timeseries)

* [Exemplo de série de tempo: Previsão sazonal](#seasonalforecasting)

* [Apêndice A - Guia do RStudio](#appendixa)

* [Apêndice B - Leitura adicional](#appendixb)

##<a id="mlstudio"></a>Interagindo com o R no Studio de AM

Esta seção apresenta algumas noções básicas de interação com o R no ambiente do Studio de AM.  A linguagem R fornece uma ferramenta poderosa para a criação de análises personalizadas e módulos de manipulação de dados dentro do ambiente de AM do Azure.

Usarei o RStudio para desenvolver, testar e depurar o código R em pequena escala.  Em seguida, corte e cole este código no módulo Excutar Script R no Studio de AM que está pronto para ser executado.  

###O módulo Executar Script R

No Studio de AM, os scripts R são executados dentro do módulo Executar Script R.  Um exemplo do módulo Executar Script R no Studio de AM é ilustrado na Figura 1.

 ![The ML Studio environment showing the Execute R Script module selected][1]

*Figura 1. O ambiente do Studio de AM mostrando o módulo Executar Script R selecionado.

Consultando a Figura 1, vejamos algumas das principais partes do ambiente do Studio de AM para trabalhar com o módulo Executar Scripts R.

- Os módulos do teste são mostrados no painel central.

- A parte superior do painel direito contém uma janela para exibir e editar seus scripts de R.  

- A parte inferior do painel direito mostra algumas propriedades do Executar Script R. Você pode exibir os logs de erro e de saída clicando nos pontos apropriados deste painel.

É claro, abordaremos o Executar Script R mais detalhadamente no restante deste documento.

Ao trabalhar com funções R complexas, é recomendável que você edite, teste e depure no RStudio. Assim como acontece com qualquer desenvolvimento de software, estenda o código de forma incremental e teste-o em pequenos casos de teste simples. Em seguida, recorte e cole suas funções na janela de script R do módulo Executar Script R.  Essa abordagem permite aproveitar o IDE RStudio e a potência da AM do Azure.  

####Executar código R

Qualquer código R no módulo Executar Script R será executado quando você clicar no botão **Executar** para iniciar o teste.  Quando a execução for concluída, uma marca de seleção aparecerá no ícone Executar Script R.

####Codificação R defensiva para a AM do Azure

Se você estiver desenvolvendo o código R para, digamos que um serviço web que usa a AM do Azure ML, você definitivamente precisa planejar como seu código irá lidar com exceções e com a entrada de dados inesperados. Para ficar claro, eu não incluí muito em relação a verificação ou a manipulação de exceção na maioria dos exemplos de código mostrados.  No entanto, a medida que prosseguimos irei dar vários exemplos de funções usando o recurso de manipulação de exceção do R.  

Se for necessário um tratamento mais completo de tratamento de exceção R recomendo a leitura das seções aplicáveis do livro de Wickham listado no [Apêndice B - Leitura adicional](#appendixb).


####Depurando e testando R no Studio de AM

Para reiterar, é recomendável testar e depurar seu código R em pequena escala no RStudio.  No entanto, há casos em que você precisará accompanhar problemas de código de R no próprio Executar Script R.  Além disso, é recomendável verificar os resultados no Studio de AM.

A saída da execução do seu código R e na plataforma AM do Azure é encontrada principalmente em output.log. Algumas informações adicionais serão vistas no error.log.  

Se ocorrer um erro no Studio de AM durante a execução de seu código R, o primeiro curso de ação deve ser verificar error.log. O arquivo error.log pode conter mensagens de erro úteis para ajudá-lo a entender e corrigir o erro.  Para exibir o arquivo error.log, clique em **Exibir log de erro** no painel **Propriedades** do Executar Script R que contém o erro.

Por exemplo, eu executei o seguinte código R, com uma variável y indefinida, em um módulo Executar Script R:

```r
x <- 1.0
z <- x + y
```

Esse código não foi executado resultando em uma condição de erro. Clicando no **Exibir log de erro** no **painel propriedades** a exibição é produzida na Figura 2:

  ![Error message pop up][2]

*Figura 2. A mensagem de erro é exibida.*

Parece que precisamos examinar o arquivo output.g para ver a mensagem de erro de R. Clique em Executar Script R e, em seguida, clique no item**Exibir output.log** no **painel propriedades** à direita. Abre uma nova janela do navegador e posso ver o seguinte:


	[ModuleOutput] [1] 14000
	[ModuleOutput]
	[ModuleOutput] Loading objects:
	[ModuleOutput]
	[ModuleOutput]   port1
	[ModuleOutput]
	[ModuleOutput] [1] "Loading variable port1..."
	[ModuleOutput]
	[ModuleOutput] Error in eval(expr, envir, enclos) : object 'y' not found

Essa mensagem de erro contém surpresas e claramente identifica o problema.

Para inspecionar o valor de qualquer objeto em R, você pode imprimir esses valores no arquivo output.log.  As regras para examinar os valores de objeto são essencialmente as mesmas de uma sessão interativa de R.  Por exemplo, se você digitar um nome de variável em uma linha, o valor do objeto será impresso no arquivo de output.log.  

####Pacotes no Studio de AM

A AM do Azure vem com mais de 350 pacotes R pré-instalados.  Você pode usar o seguinte código no módulo Executar Script R para recuperar uma lista dos pacotes pré-instalados.

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Se você não entende a última linha do código no momento, continue lendo.  No restante deste documento, discutiremos extensivamente usando R no ambiente da AM do Azure.

###	Introdução ao RStudio

O RStudio é um ambiente de desenvolvimento integrado (IDE) para R amplamente utilizado. Usarei o RStudio para editar, testar e depurar alguns códigos R usados neste Guia de Início Rápido.  Depois que o código R for testado e estiver pronto, você simplesmente recorta do editor do RStudio e cola no módulo Executar Script R do Studio de AM.  

Se você não tiver R instalado em seu computador desktop, recomendo que você faça isso agora. Downloads gratuitos do R de código-fonte aberto estão disponíveis na rede de arquivamento abrangente R ou CRAN em http://www.r-project.org/. Há downloads disponíveis para Windows, MacOS e UNIX/Linux. Escolha um espelho próximo e siga as instruções de download.   Além disso, CRAN contém uma grande quantidade de pacotes de manipulação de dados e análise úteis.

Se você for novo no RStudio, você deve baixar e instalar a versão para desktop. Você pode encontrar os downloads do RStudio para Windows, MacOS e Linux/UNIX em http://www.rstudio.com/products/RStudio/. Siga as instruções fornecidas para instalar o RStudio em seu computador desktop.  

Uma tutorial de introdução no RStudio está disponível em https://support.rstudio.com/hc/pt-br/sections/200107586-Using-RStudio.

Posso fornecer algumas informações adicionais sobre como usar o RStudio no [Apêndice A][appendixa].  

##<a id="scriptmodule"></a>Obtendo dados e de entrada e saída do módulo Executar Script R

Nesta seção, discutiremos como você obtém dados de entrada e saída do módulo Executar Script R.  Analisaremos como lidar com vários tipos de dados de leitura de entrada e saída do módulo Executar Script R.

O código completo para esta seção está no arquivo zip baixado anteriormente.

###Carregamento e verificação de dados no Studio de AM

####<a id="loading"></a>Carregando o conjunto de dados

Vamos começar com o carregamento do arquivo **csdairydata.csv** ao nosso Studio de AM do Azure.

- Inicie o ambiente do Studio de AM do Azure.

- Clique em + no canto inferior esquerdo da tela e selecione **Conjunto de dados**.

- Selecione o arquivo usando **Procurar**.

- Verifique se você selecionou **arquivo CSV genérico com cabeçalho (.csv)**.

- Clique na marca de seleção.

- Você deve ver os novos conjuntos de dados ao clicar na guia **Conjuntos de dados**.  

####Criando um teste

Agora que temos alguns dados no Studio de AM, precisamos criar uma experiência para fazer a análise.  

- Clique em + na parte inferior esquerda e selecione **Teste**

- Dê um título ao seu teste.  Chamarei minha experiência **Análise derivados de leite Califórnia**.

- Procure o conjunto de dados que acabou de carregar.

- Arraste e solte o **conjunto de dados csdairydata.csv** no teste.  

- Na caixa **Procurar itens de teste** na parte superior do painel à esquerda, digite **Executar Script R**.  O módulo irá aparecer na lista de pesquisa.

- Arraste e solte o módulo Executar Scripts R em sua palete.  

- Conecte a saída do **conjunto de dados csdairydata.csv** na entrada mais à esquerda (**Dataset1**) do **Executar Script R**.

- **Não se esqueça de clicar em "Save"!**

Agora seu teste deve ser similar a Figura 3.

![The CA Dairy Analysis Experiment with dataset and Execute R Script module][3]

*Figura 3.  A experiência de análise de produtos derivados de leite da Califórnia com o conjunto de dados e o módulo Executar Script R.*

####Verificar os dados

Vamos dar uma olhada nos dados que carregou em nosso teste.  No teste, clique duas vezes na saída do **conjunto de dados cadairydata.csv** e selecione **visualizar**.   Você deve ver algo semelhante à Figura 4.  

![The summary of the cadairydata.csv data set][4]

*Figura 4.  Resumo de conjunto de dados cadairydata.csv.*

Nessa exibição, vemos muitas informações úteis.  A linha **Tipo de Recurso** mostra quais tipos de dados o Studio de AM do Azure atribui às colunas do nosso conjunto de dados.  Também vemos as primeiras linhas do conjunto de dados.  Dar uma olhada rápida como esta é boa uma verificação de integridade para ser feita antes de começar qualquer trabalho sério.

###	Primeiro Script R

Vamos criar um primeiro script R simples para testar no Studio de AM do Azure.  Eu criei e testei o seguinte script no RStudio:  

```r
## Only one of the following two lines should be used
## If running in ML Studio use the first line with maml.mapInputPort().
## If in RStudio used the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure ML Studio.
maml.mapOutputPort('cadairydata')
```

Agora preciso transferir esse script para o meu Studio de AM do Azure.  Eu poderia simplesmente recortar e colar.  No entanto, nesse caso, eu vou transferir o meu script R por meio de um arquivo zip.

###	Entrada de dados para o módulo Executar Script R

Vamos dar uma olhada nas entradas para o módulo Executar Script R.  Neste exemplo, vamos ler os dados dos produtos derivados de leite da Califórnia no módulo Executar Script R.  

Há três entradas possíveis para o módulo Executar Script R.  Você pode usar qualquer uma ou todas essas entradas, dependendo do seu aplicativo.  Também é totalmente aceitável usar um script R que não recebe nenhuma entrada.  

Vamos examinar cada uma dessa entradas da esquerda para a direita. Você pode ver os nomes de cada uma das entradas colocando o cursor sobre a entrada e lendo a dica de ferramenta.  

####	Pacote de script

A entrada de Pacote de script permite que você passe o conteúdo de um arquivo zip para o módulo Executar Script R.  Você pode usar um dos comandos a seguir para ler o conteúdo do arquivo zip em seu código R:

```r
source("src/yourfile.R") # Reads an zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [WACOM.NOTE] A AM do Azure trata arquivos zip como se eles estivessem no diretório src/, por isso, é necessário prefixar seus nomes de arquivo com este nome de diretório.  

Já discutimos sobre conjuntos de dados de carregamento em [Carregando o conjunto de dados](#loading).  Depois de ter criado e testado o script R mostrado na seção anterior, faça o seguinte:

1. Salve o script R em um arquivo .R.  Eu chamo meu arquivo script "simpleplot.R".  

2.  Crie um arquivo zip e copie o script no arquivo zip.

3.	Adicione o arquivo n o **conjuntos de dados** no Studio de AM, especificando o tipo como **zip**. Agora você deve ver o arquivo zip em seus conjuntos de dados.

4.	Arraste e solte o arquivo zip do **conjuntos de dados** até as **telas do Studio de AM**.

5.	Conecte a saída do ícone **Dados zip** no o **Pacote de script** entrada do módulo **Executar Script R**.

6.	Digite a função 'source()' com o nome do arquivo zip na janela de código no o módulo **Executar Script R**.  No meu caso, digitei 'source("src/SimplePlot.R")'.  

7.	Lembre-se de clicar em **Salvar**.

Uma vez concluídas essas etapas, o módulo Executar Script R executará o script R no arquivo zip, quando o teste for executado. Agora seu teste deve ser similar a Figura 5.

![Experiment using zipped R script][6]

*Figura 5. Experimente usar R scritp zipado.*

####Dataset1

Você pode passar uma tabela retangular de dados para seu o código R usando a entrada Dataset1.  Em nosso script simples a função "maml.mapInputPort(1)" lê os dados da porta 1.  Esses dados são atribuídos a um nome de variável do quadro de dados em seu código.  Em nosso script simples a primeira linha de código realiza a atribuição.

```r
cadairydata <- maml.mapInputPort(1)
```

Execute seu teste clicando no botão **Executar**.  Quando a execução for concluída, clique no módulo Executar Script R clique em**Exibir log de saída** no painel propriedades.  Uma nova página deve aparecer em seu navegador exibindo o conteúdo do arquivo de output.log.  Quando você rolar para baixo, você deve ver algo como o seguinte:

	[ModuleOutput] [1] "Loading variable port1..."
	[ModuleOutput]
	[ModuleOutput] 'data.frame':	228 obs. of  9 variables:
	[ModuleOutput]
	[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
	[ModuleOutput]
	[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
	[ModuleOutput]
	[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
	[ModuleOutput]
	[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
	[ModuleOutput]
	[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
	[ModuleOutput]
	[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
	[ModuleOutput]
	[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
	[ModuleOutput]
	[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
	[ModuleOutput]
	[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Esses resultados são como o esperado, com 228 observações e 9 colunas no dataframe.  Podemos ver os nomes de coluna, o tipo de dados R e um exemplo de cada coluna.

> [WACOM.NOTE] Essa mesma saída impressa está convenientemente disponível na saída do dispositivo R do módulo Executar Script R.  Discutiremos as saídas do módulo R executar na próxima seção.  

####Dataset2

O comportamento da entrada do Dataset2 é idêntico ao do Dataset1.  Usando essa entrada, você pode passar uma segunda tabela retangular de dados para o seu código R.  A função "maml.mapInputPort(2)", com o argumento 2, é usada para passar esses dados.  

###Execute as saídas do Script R

####Exibir um dataframe

Você pode exibir o conteúdo de um dataframe R como tabela retangular por meio da porta de resultados do Dataset1 usando a função "maml.mapOutputPort()".  Em nosso script R simples, isso é feito pela linha a seguir:

```r
maml.mapOutputPort('cadairydata')
```

Depois de executar o teste, clique na porta de saída do resultado do Dataset1 e clique em **Visualizar**.  Você deve ver algo como na Figura 6.

![The visualization of the output of the California dairy data][7]

*Figura 6. A visualização da saída dos dados dos derivados de leite da Califórnia *

Esta saída parece idêntica à entrada, exatamente como se esperava.  

###	Saída do Dispositivo R

A saída do Dispositivo do módulo Executar Script R contém mensagens e saída de gráficos.  As duas mensagens de erro padrão e de saída padrão de R são enviadas para a porta de saída do dispositivo R.  

Para exibir a saída do dispositivo R, clique na porta e, em seguida, em **Visualizar**.  Podemos ver a saída padrão e o erro padrão do script R na Figura 7.

![Standard output and standard error from the R Device port][8]

*Figura 7. Saída padrão e erro padrão da porta do Dispositivo R.*

Rolando para baixo podemos ver a saída de gráficos do nosso script R na Figura 8.  

![Graphics output from the R Device port][9]

*Figura 8. Saída de gráficos da porta do Dispositivo R.*  

##<a id="filtering"></a>Filtragem de dados e a transformação

Nesta seção, vamos executar alguns dados básicos de filtragem e operações de transformação nos dados de produtos derivados de leite da Califórnia.  No final desta seção, teremos dados em um formato adequado para a criação de um modelo de análise.  

Mais especificamente, nesta seção vamos executar várias tarefas de transformação e de limpeza de dados comuns; transformação de tipo, filtro por dataframes, adicionar novas colunas computadas e transformações de valor. Este histórioco deve ajudá-lo a lidar com as diversas variações encontradas em problemas do mundo real.

O código completo R para esta seção está disponível no arquivo zip baixado anteriormente.

###	Transformações de tipo

Agora que podemos ler os dados dos derivados de leite da Califórnia no código R no módulo Executar Script R, precisamos garantir que os dados nas colunas têm o tipo e o formato desejado.  

R é uma linguagem tipificada dinamicamente, o que significa que os tipos de dados são forçados entre si quando necessário. Os tipos de dados atômicos em R incluem numérico, lógico e de caractere.  O fator de tipo é usado para o armazenamento de dados categóricos de forma compacta.  Você pode obter mais informações sobre tipos de dados nas referências no [Apêndice B - Leitura adicional](#appendixb).

Quando dados tabulares são lidos em R de a partir de uma fonte externa, é sempre uma boa idéia verificar os tipos resultantes nas colunas.  Você pode desejar uma coluna de tipo de caractere, mas em muitos casos isso aparecerá como fator ou vice-versa.  Em outros casos, uma coluna que você acha que deve ser numérica é representadoapor dados de caracteres, por exemplo, "1.23" em vez de 1.23 como número de ponto flutuante.  

Felizmente, é fácil converter de um tipo para outro, desde que o mapeamento seja possível.  Por exemplo, você não pode converter "Nevada" para um valor numérico, mas você pode convertê-la em um fator (variável categórica).  Um outro exemplo, você pode converter um numérico 1 em um caractere "1" ou um fator.  

A sintaxe para qualquer uma dessas conversões é simples: "as.datatype()".  Essas funções de conversão de tipo incluem o seguinte:

* `as.numeric()`

* `as.character()`

* `as.logical()`

* `as.factor()`

Examinar os tipos de dados das colunas de entrada, na seção anterior. Todas as colunas são do tipo numérico, exceto para a coluna rotulada "Month", que é o caractere de tipo.  Vamos converter isso em um fator e os resultados do teste.  

Posso ter excluído a linha que criou a matriz scatterplot e adicionou uma linha para converter a coluna "Month" em um fator.  Em meu teste, eu vou simplesmente recortar e colar o código R na janela de código do módulo Executar Script R.  Você também pode atualizar o arquivo zip e carregá-lo no Studio de AM do Azure, mas isso requer várias etapas.  

```r
## Only one of the following two lines should be used
## If running in ML Studio use the first line with maml.mapInputPort().
## If in RStudio used the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure ML Studio.
maml.mapOutputPort('cadairydata')
```

Vamos executar esse código e examinar a saída. A saída de **Visualizar** no menu do dispositivo R é mostrado na Figura 9.

![Summary of the dataframe with a factor variable][10]

*Figura 9. Resumo do dataframe com um fator variable.*

O tipo de mês deve agora dizer "**Fator c/ 14 níveis**".  Isso é um problema, pois há apenas 12 meses no ano. Você também pode verificar se o tipo no **Visualizar** da porta do conjunto de dados de resultado "**Categórico**".

O problema é que a coluna "Month" não foi codificada sistematicamente.  Em alguns casos, um mês é chamado abril e em outros é abreviado como abr. Podemos resolver esse problema, reduzindo a cadeia de caracteres para três caracteres.  Agora, a linha de código deve ser assim:

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Executar novamente o teste e **Visualizar** a saída da porta do dispositivo R com os resultados mostrados na Figura 10.  

![Summary of the dataframe with correct number of factor levels][11]

*Figura 10. Resumo do dataframe com o número correto de níveis de fator.*

Nossa variável fator agora tem os 12 níveis desejados.

###Filtragem de dataframe básico

Os dataframes R oferecem suporte a recursos avançados de filtragem.  Conjuntos de dados podem ser subdivididos usando filtros lógicos em linhas ou colunas. Em muitos casos, serão necessários critérios complexos de filtro. As referências no [Apêndice B - Leitura adicional](#appendixb) contêm exemplos extensivos de dataframes de filtragem.  

Há algumas filtragens que devemos fazer em nosso conjunto de dados. Se você examinar as colunas no dataframe cadariydata, você verá duas colunas desnecessárias.  A primeira coluna contém apenas um número de linha que não é muito útil. A segunda coluna, Year.Month, contém informações redundantes.  Podemos facilmente excluir essas colunas usando o seguinte código R.

> [WACOM.NOTE] De agora em diante nesta seção, só mostrarei o código adicional que estou adicionando no módulo Executar Script R.  Vou adicionar cada nova linha **antes** da função "str()". Posso usar essa função para verificar os resultados no Studio de AM do Azure.

Adiciono a seguinte linha ao meu código R no módulo Executar Script R.

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Execute esse código em sua experiência e verifique o resultado com o**Visualizar** na porta do Dispositivo R. Esses resultados são mostrados na Figura 11.

![The summary of the dataframe with two columns removed][12]

*Figura 11.  Resumo do dataframe com duas colunas removidas.*

Boas notícias! Podemos obter os resultados esperados.

###Adicionando uma nova coluna

Para criar modelos de série de tempo é conveniente ter uma coluna que contenha os meses desde o início da série de tempo. Criaremos uma nova coluna "Month.Count".

Para ajudar a organizar o código, vamos criar nossa primeira função simples, "num.month()".  Em seguida, aplicamos essa função para criar uma nova coluna no dataframe. O novo código é assim:

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series.
num.month <- function(Year, Month) {
  ## Find the starting year.
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series.
  12 * (Year - min.year) + Month - 1
}

# Compute the new column for the dataframe.
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Agora execute o teste atualizado e use o **Visualizar** na porta do Dispositivo R para exibir os resultados. Esses resultados são mostrados na Figura 12.

![Summary of the dataframe with the additional column][13]

*Figura 12. Resumo do dataframe com a coluna adicional.*

Parece que tudo está funcionando.  Temos a nova coluna com os valores esperados em nosso dataframe

###Transformações de valor

Nesta seção, vamos executar algumas transformações simples nos valores de algumas das colunas de nosso dataframe.  A linguagem R suporta transformações de valor quase que de forma arbitrária.  As referências no [Apêndice B - Leitura adicional](#appendixb) contém exemplos detalhados.

Se você examinar os valores nos resumos ds nosso datafame você verá algo estranho. Há mais sorvetes do que leite produzido na Califórnia?  Não, certamente não, pois isso não faz sentido, além de triste pois alguns de nós amam sorvetes. As unidades são diferentes. O preço está em unidades de libras dos EUA, leite está em unidades de 1 M de libras dos EUA, sorvete está em unidades de 1.000 galões dos EUA e queijo fresco está em unidades de 1.000 libras dos EUA. Supondo que o sorvete pese aproximadamente 6,5 libras por galão, podemos facilmente fazemos a multiplicação para converter esses valores para que todos eles estejam em unidades iguais de 1.000 libras.

Para o nosso modelo de previsão, usamos um modelo de multiplicação de tendência e ajuste sazonal desses dados. Uma transformação de log nos permite usar um modelo linear, simplificando esse processo.  Podemos aplicar a transformação de log na mesma função em que o multiplicador será aplicado.

No código a seguir, eu defino uma nova função "log.transform()" e a aplico nas linhas que contêm os valores numéricos. A função "Map()" de R é usada para aplicar a função "log.transform()" nas colunas selecionadas do dataframe.  "Map()" é semelhante a "apply()", mas permite mais de uma lista de argumentos para a função. Observe que uma lista de multiplicadores fornece o segundo argumento para a função "log.transform()". A função "na.omit()" é usada como um pouco de limpeza para garantir que não possuem valores ausentes ou indefinidos no dataframe.

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments.
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch.
  ## If there is an exception, print the warningmessage to
  ## standard error and return NA.
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data.
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

Há várias coisas acontecendo na função "log.transform()".  A maioria dos códigos está verificando se há possíveis problemas com os argumentos ou está lidando com exceções que ainda podem surgir durante os cálculos. Apenas algumas linhas desse código realmente fazem os cálculos.

O objetivo da programação de defesa é evitar a falha de uma única função impedindo o processamento de continuar.  Uma falha abrupta de uma análise de longa execução pode ser muito frustrante para os usuários. Para evitar essa situação, os valores retorno padrão devem ser escolhidos o que limita os danos ao processamento downstream.  Uma mensagem também é emitida para alertar os usuários que algo deu errado.

Se você não está acostumado a programação de defesa em R, esses códigos podem parecer um pouco confusos. Eu o orientarei durante as etapas principais:

1. Um vetor de quatro mensagens é definido, as quais são usadas para comunicar informações sobre alguns dos possíveis erros e exceções que podem ocorrer com esse código.

2.  Posso retornar um valor NA para cada caso.  Há muitas outras possibilidades que podem ter menos impacto.  Eu poderia retornar um vetor de zeros ou o vetor de entrada original, por exemplo.

3.  As verificações são executadas nos argumentos para a função.  Em cada caso, se for detectado um erro, será retornado um valor padrão e uma mensagem é produzida pela função "warming()". Estou usando "warning()" em vez de "Stop ()", pois o último encerrará a execução, exatamente o que eu estou tentando evitar. Observe que eu escrevi este código em um estilo de procedimento, pois nesse caso uma abordagem funcional me parece complexa e obscura.

4.  O log de computações são encapsuladas em "tryCatch()" para que as exceções não causem uma interrupção abrupta no processamento. Sem "tryCatch()" a maioria dos erros gerados pelas funções R resulta em um sinal de parada, que faz exatamente isso.

Executar esse código R em seu teste e veja a saída impressa no output.log.  Agora, você verá os valores transformados das quatro colunas usando o **Visualizar** no menu do dispositivo R conforme mostrado na Figura 13.

![The summary of the transformed values in the dataframe][14]

*Figura 13. Resumo dos valores transformados no dataframe.*

Vemos que os valores foram transformados.  Agora, a produção de leite excede bastante a produção de todos os outros produtos derivados do leite, lembrando que agora estamos analisando uma escala logarítmica.

Neste momento, os dados são limpos e estamos prontos para a modelagem.  Observando o resumo de visualização da saída do conjunto de dados de resultado do nosso módulo Executar Script R, você verá que a coluna "Month" é "Categorica" com 12 valores exclusivos, novamente, da forma que desejávamos.

##<a id="timeseries"></a>Objetos de série de tempo e análise de correlação

Nesta seção vamos explorar alguns objetos básicos de série de tempo de R e vamos analisar as correlações entre algumas das variáveis. Nosso objetivo é obter um dataframe de saída que contenha as informações de correlação de pares em várias defasagens.

O código R completo para esta seção está disponível no arquivo zip baixado anteriormente.

###Objetos de série de tempo em R

Como já mencionado, as série de tempo são uma série de valores de dados indexados por tempo.  Objetos de série de tempo R são usados para criar e gerenciar o índice de tempo. Há diversas vantagens em usar objetos de série de tempo. Objetos de série de tempo de livram-no de muitos detalhes de gerenciamente os valores de índice de série de tempo os quais são encapsulados no objeto. Além disso, os objetos de série de tempo permitem que você use vários métodos de série de tempo para plotar, imprimir, modelar etc.

A classe de série de tempo POSIXct é comumente usada e é relativamente simples. Essa classe de série de tempo mede o tempo a partir do início da época, 1º de janeiro de 1970.  Vamos usar objetos de série de tempo POSIXct neste exemplo. Outras classes de objeto de série de tempo amplamente utilizados em R incluem zoo e xts, série temporal extensível.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

###	Exemplo de objeto de série de tempo

Vamos começar com o nosso exemplo. Arraste e solte um **novo módulo Executar Script R**no seu testes.  Conecte a porta de saída do conjunto de dados de resultado 1 do módulo Executar R Script existente na porta de entrada do conjunto de dados 1 do novo módulo Executar Script R.

Como fiz no primeiro exemplo, enquanto progredimos no exemplo, em alguns pontos só mostrarei as linhas adicionais incrementais de código de R em cada etapa.  

####	Lendo o Dataframe

Como primeira etapa, vamos ler um dataframe e verificar se obtemos os resultados esperados. O código a seguir deve fazer isso:

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Agora, execute o teste.  A saída que você observa na porta do dispositivo R deve ser como a Figura 14.

![Summary of the dataframe in the Execute R Script module][15]

*Figura 14. Resumo do dataframe no módulo Executar Script R.*

Esses dados são de tipos e formato esperados.  Observe que a coluna "Month" é do fator tipo e tem o número esperado de níveis.

####Criando um objeto de série de tempo

Precisamos adicionar um objeto de série de tempo ao nosso dataframe.  Substitua o código atual com o seguinte, o qual adiciona uma nova coluna da classe POSIXct.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Agora, verifique a saída do dispositivo R. Deve se parecer com a Figura 15.

![Summary of the dataframe with a time series object][16]

*Figura 15. Resumo do dataframe com umo objeto de série de tempo.*

Podemos ver o resumo a nova coluna é de fato da classe POSIXct.

###Explorando e transformando os dados

Vamos explorar algumas das variáveis neste conjunto de dados.  Uma matriz de scatterplot é uma boa maneira para darmos uma olhada rápida. Eu estou substituindo a função "str()" no código anterior R pela seguinte linha:

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Execute esse código e veja o que acontece.  A plotagem produzida na porta do dispositivo R deve parecer com a Figura 16.

![Scatterplot matrix of selected variables][17]

*Figura 16. Matriz de Scatterplot de variáveis selecionadas.*

Há algumas estruturas de aparência estranha nas relações entre essas variáveis.  Talvez isso surja de tendências nos dados e do fato de que não padronizamos as variáveis.

###Análise de correlação

Para executar análise de correlação, precisamos padronizar e eliminar tendências das variáveis. Poderíamos apenas usamos a função "scale ()" de R que centraliza e dimensiona as variáveis. Essa função também pode ser executada mais rapidamente.  No entanto, eu quero mostrar um exemplo de programação defensiva em R.

A função "ts.detrend()" mostrada abaixo realiza essas duas dessas operações. As duas linhas de código a seguir eliminam os dados de tendência e, em seguida, padronizam os valores.

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to detrend and standardize a time series.

  ## Define some messages if they are NULL.  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases.
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit.
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit.
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit.
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct.
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## Detrent the time series using a linear model.
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest.
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships.
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

Tem muita coisa acontecendo na função "ts.detrend()".  A maioria dos códigos está verificando se há possíveis problemas com os argumentos ou está lidando com exceções que ainda podem surgir durante os cálculos. Apenas algumas linhas desse código realmente fazem os cálculos.

Já abordamos um exemplo de programação de defesa em [Transformações de valor](#valuetransformations). Os dois blocos de computação estão encapsulados em "tryCatch()".  Para alguns erros, faz sentido retornar ao vetor original de entrada e, em outros casos, retornar a um vetor de zeros.  

Observe que a regressão linear usada para a eliminação de tendência é uma regressão de uma série de tempo. A variável preditora é um objeto de série de tempo.  

Quando "ts.detrend()" é definido, aplicamos as variáveis de interesse em nosso dataframe. Devemos forçar a lista resultante criada pelo "lapply()" nos dados do dataframe usando "as.data.frame()".  Devido aos aspectos de defesa do "ts.detrend()", falha ao processar uma das variáveis não impedirá o processamento correto dos outros.  

A última linha do código cria um par scatterplot. Após executar o código R os resultados do scatterplot são mostrados na Figura 17.

![Pairwise scatterplot of de-trended and standardized time series][18]

*Figura 17. Emparelhe o scatterplot da séries de tempo padronizada e sem tendências.*

Você pode comparar esses resultados aos mostradas na Figura 17. Com a tendência removida e as variáveis padronizadas, vemos muito menos estrutura nas relações entre essas variáveis.

O código para calcular as correlações como objetos ccf R é assim:

```r
## A function to compute pairwise correlations from a
## list of time series value vectors.
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwaise indices.
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects.
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Executar esse código produz o resultado mostrado na Figura 18.

![List of ccf objects from the pairwise correlation analysis][19]

*Figura 18. Lista de objetos ccf de análise da correlação emparelhadas.*

Há um valor de correlação para cada intervalo.  Nenhum desses valores de correlação é grande o suficiente para ser significativo. Podemos, portanto, concluir que é possível modelar cada variável de forma independente.

###Exibir um dataframe

Podemos ter calculado as correlações emparelhadas como uma lista de objetos ccf R. Isso apresenta um pequeno problema, pois a porta de saída do conjunto de dados resultado exige um dataframe.  Além disso, o objeto ccf é uma lista e queremos apenas os valores no primeiro elemento dessa lista, as correlações em vários intervalos.

O código a seguir extrai os valores de intervalo da lista de objetos ccf, que são listas:

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names.
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line only works in Azure ML.
## when running in RStudio, this code will result in an error.
#maml.mapOutputPort('outframe')
```

A primeira linha de código é um pouco complicada e algumas explicações podem ajudá-lo a entendê-lo. Trabalhando de dentro para fora, temos o seguinte:

1.  O "**[[**"operator com o argumento "**1**", seleciona o vetor de correlações nos intervalos do primeiro elemento da lista de objetos ccf.

2.  A função "do.call()" aplica-se a função "rbind()" nos elementos de lista retorna por "lapply()".

3.  A função "data.frame()" converte o resultado produzido por "do.call()" para um dataframe.

Observe que os nomes das linhas estão na coluna do dataframe. Isso preserva os nomes de linha quando eles são o resultado do Executar Script R.

A execução do código produz o resultado mostrado na Figura 29 quando eu **Visualizar** a saída da porta do conjunto de dados do resultado. Os nomes de linha estão na primeira coluna conforme o esperado.

![Results output from the correlation analysis][20]

*Figura 29. Resultados da saída da análise de correlação.*

##<a id="seasonalforecasting"></a>Exemplo de série de tempo: Previsão sazonal

Nossos dados agora estão em um formato adequado para análise e determinamos que não há nenhuma correlação significativa entre as variáveis.  Vamos continuar e criar um modelo de previsão de série de tempo.  Usando esse modelo vamos prever a produção de leite da Califórnia para os 12 meses de 2013.

Nosso modelo de previsão terá dois componentes, um componente de tendência e um componente sazonal.  A previsão concluída é o produto desses dois componentes.  Esse tipo de modelo é conhecido como modelo de multiplicação. A alternativa é um modelo de adição. Nós já aplicamos uma transformação logarítmica para as variáveis de interesse que torna essa análise manejável.

O código R completo para esta seção está disponível no arquivo zip baixado anteriormente.

###	Criando o dataframe para análise

Comece adicionando um **novo módulo Executar Script R** no seu teste.  Conecte uma saída **conjunto de dados de resultado** do módulo Executar R Script existente na entrada do **Dataset1** do novo módulo.  O resultado deve ser semelhante a Figura 20.

![The experiment with the new Execute R Script module added][21]

*Figura 20. O teste com o novo módulo adicionado Executar Script R.*

Como a análise de correlação que acabamos de concluir, precisamos adicionar uma coluna com um objeto de série de tempo POSIXct. O código a seguir fará exatamente isso:

```r
# If running in ML Studio uncomment the first line with maml.mapInputPort().
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Execute esse código e examine a porta de saída do dispositivo R. O resultado deve ser semelhante a Figura 21.

![A summary of the dataframe][22]

*Figura 21. Resumo do dataframe.*

Com esse resultado, você está pronto para iniciar nossa análise.

###Criar um conjunto de dados de treinamento

Com o dataframe construído, precisamos criar um conjunto de dados de treinamento. Esses dados incluirão todas as observações exceto as últimas 12, do ano de 2013, as quais são o nosso conjunto de dados de teste. O seguinte código subagrupa o dataframe e cria plotagens de produção de derivados de leite e de as variáveis de preço. Então, eu crio plotagens das quatro produções e variáveis de preços. Uma função anônima é usada para definir alguns argumentos para a plotagem e, em seguida, para iterar a lista dos outros dois argumentos com "Map()". Se você estiver pensando que um loop funcionaria bem aqui, você está certo. Mas, como R é uma linguagem funcional, estou lhe mostrando uma abordagem funcional.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Executar o código produz séries de plotagens de série de tempo da saída do dispositivo R mostrada na Figura 22. Observe que o eixo de tempo é em unidades de datas, uma bela vantagem do método de plotagem de série de tempo.

![First of time series plots of California dairy production and price data](./media/machine-learning-r-quickstart/unnamed-chunk-161.png)

![Second of time series plots of California dairy production and price data](./media/machine-learning-r-quickstart/unnamed-chunk-162.png)

![Third of time series plots of California dairy production and price data](./media/machine-learning-r-quickstart/unnamed-chunk-163.png)

![Fourth of time series plots of California dairy production and price data](./media/machine-learning-r-quickstart/unnamed-chunk-164.png)

*Figura 22. Plotagens de série de tempo dos dados de e preço e da a produção de derivados de leite da Califórnia. *

###	Um modelo de tendência

Depois de criar um objeto de série de tempo e de ter examinado os dados, vamos começar a construir um modelo de tendência para os dados de produção leite da Califórnia. Podemos fazer isso com uma regressão da série de tempo.  No entanto, fica claro na plotagem, que é necessário mais do que uma inclinação e uma interceptação para modelar com precisão a tendência observada nos dados de treinamento.

Dada uma pequena escala de dados, vou criar o modelo para a tendência no RStudio e, em seguida, vou recortar e colar o modelo resultante na AM do Azure. O RStudio fornece um ambiente adequado para esse tipo de análise interativa.

Como primeira tentativa, tentarei uma regressão polinomial com poderes de até 3.  Existe um perigo real de sobreajuste desses tipos de modelos. Portanto, é melhor evitar condições de ordem mais alta.  A função "I()" inibe a interpretação do conteúdo (interpreta o conteúdo "como está") e permite que você grave uma função interpretada literalmente em uma equação de regressão.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Isso gera o seguinte:

```
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,	Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```	

Dos valores de P (Pr(>|t|)) nessa saída, podemos ver que o termo ao quadrado pode não ser significativo.  Vou usar a função "Update ()" para modificar esse modelo eliminado o termo ao quadrado.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Isso gera o seguinte:

```
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

Assim parece melhor.  Todos os termos são significativos.  No entanto, o valor de 2e-16 é um valor padrão e não deve ser levado muito a sério.  

Para teste de sensatez, vamos criar uma plotagem de série de tempo dos dados da produção de derivados de leite da Califórnia com a curva de tendência mostrada.  Adicionei o seguinte código no modelo Executar Script R da AM do Azure (não no RStudio) para criar o modelo e fazer uma plotagem.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![CA milk production data with trend model shown](./media/machine-learning-r-quickstart/unnamed-chunk-18.png)

*Figura 23. Dados de produção leite da Califórnia com o modelo de tendência mostrado.*

Parece que o modelo de tendência se ajusta aos dados muito bem. Além disso, não parece ter evidência de ajuste excessivo, como agitações estranhas na curva de modelo.  

###Modelo sazonal

Com um modelo de tendência em mãos, precisamos presssionar e incluir efeitos sazonais. Nós usaremos o mês do ano como uma variável fictícia no modelo linear para capturar o efeito de mês a mês. Observe que quando você introduz variáveis fator em um modelo, a interceptação não deve ser computada.  Se você não fizer isso, a fórmula será muito especificada e R descartará um dos fatores desejados, mas manterá o termo de interceptação.

Como temos um modelo de tendência satisfatório, podemos usar a função "Update ()" para adicionar novos termos ao modelo existente. -1 na atualização da fórmula descarta o termo de interceptação.  Continuando em RStudio:

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Isso gera o seguinte:

```
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,	Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

Vemos que o modelo não tem um termo de interceptação e tem 12 fatores mês significativos.  Isso é exatamente o que queremos.

Vamos fazer outra plotagem de série de tempo dos dados de produção de derivados de leite da Califórnia para ver até que ponto o modelo sazonal está funcionando.  Adicionei o seguinte código no modelo Executar Script R da AM do Azure (não no RStudio) para criar o modelo e fazer uma plotagem:

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Executar esse código na AM do Azure produz a plotagem mostrada na Figura 24.

![CA milk production with model including seasonal effects](./media/machine-learning-r-quickstart/unnamed-chunk-20.png)

*Figura 24. Produção de leite na Califórnia com o modelo dos efeitos sazonais.*

O ajuste para os dados mostrados na Figura 24 é bastante encorajador.  A tendência e o efeito sazonal (variação mensal) parecem razoáveis.

Como outra verificação em nosso modelo, vamos dar uma olhada nos resíduos. O código a seguir calcula os valores previstos de nossos dois modelos, calcula os resíduos para o modelo sazonal e então plota esses resíduos nos dados de treinamento.

```r
## Compute predictions from our models.
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

A plotagem restante é mostrada na Figura 25.

![Residuals of the seasonal model for the training data](./media/machine-learning-r-quickstart/unnamed-chunk-21.png)

*Figura 25. Resíduos do modelo sazonal para os dados de treinamento. *

Esses resíduos parecem razoáveis.  Não há nenhuma estrutura em particular, exceto o efeito da recessão de 2008 e 2009, que nosso modelo não leva em conta bem.

A plotagem mostrado na Figura 25 é útil para detectar qualquer padrão dependente de tempo nos resíduos. A abordagem explícita de computação e plotagem residual que eu sei coloca os resíduos na ordem de tempo no gráfico.  Se, por outro lado, tinha plotado, "milk.lm$residuals" a plotagem não seria em ordem de tempo.

Também é possível usar "plot.lm()" para produzir uma série de gráficos de diagnóstico:

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Esse código gera uma série de gráficos de diagnóstico mostrados na Figura 26.

![First of Diagnostic plots for the seasonal model](./media/machine-learning-r-quickstart/unnamed-chunk-221.png)

![Second of Diagnostic plots for the seasonal model](./media/machine-learning-r-quickstart/unnamed-chunk-222.png)

![Third of Diagnostic plots for the seasonal model](./media/machine-learning-r-quickstart/unnamed-chunk-223.png)

![Fourth of Diagnostic plots for the seasonal model](./media/machine-learning-r-quickstart/unnamed-chunk-224.png)

*Figura 26. Gráficos de diagnóstico de gráficos para o modelo sazonal.*

Há alguns pontos de grande influencia identificados nessas representações gráficas, mas eles não causam muita preocupação. Além disso, podemos ver pela plotagem Q-Q Normal que os resíduos são próximos ao normalmente distribuídos, uma suposição importante para os modelos lineares.

###Avaliação de previsão e modelo

Tem só mais uma coisa para concluir o nosso exemplo.  Precisamos calcular as previsões e medir o erro em relação aos dados reais.  Nossa previsão será para os 12 meses de 2013. Podemos calcular uma medida de erro para essa previsão para os dados reais que não fazem parte do nosso conjunto de dados de treinamento. Além disso, podemos comparar o desempenho em 18 anos de dados de treinamento com os 12 meses de dados de teste.  

Um número de métricas é usado para medir o desempenho dos modelos de série de tempo. Em nosso caso, usaremos o erro de raiz quadrada média ou erro RMS. A função a seguir calcula o erro RMS entre duas séries:  

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors.

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments.
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
    warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy.
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models.
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe.
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Como a função "log.transform()" que discutido na seção Transformações de valor, há muita verificação de erro e exceção de código de recuperação nesta função.  Os princípios empregados são os mesmos. O trabalho é feito em dois lugares ajustados em "tryCatch()".  Em primeiro lugar, a série de tempo é de exponenciação, pois temos trabalhado com os valores dos logarítmos. Em segundo lugar, o erro real do RMS é computado.  

Equipado com uma função para medir o erro RMS, vamos criar e exibir um dataframe que contenha os erros de RMS. Incluiremos termos para o modelo de tendência sozinho e o modelo completo com fatores sazonais. O código a seguir faz o trabalho usando os dois modelos lineares que construímos:

```r
## Compute the RMS error in a dataframe.
## Include the row names in the first column so they will
## appear in the output of the Execute R Script.
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure ML Studio.
maml.mapOutputPort('RMS.df')
```

Executar esse código produz a saída mostrada na Figura 27 na porta de saída de conjunto de dados do resultado.

![Comparison of RMS error for the models][26]

*Figura 27. Comparação de erro do RMS com os modelos.*

A partir desses resultados, podemos ver que adicionando os fatores sazonais ao modelo, reduz o erro RMS significativamente.  Não é muito surpreendente, o erro RMS para os dados de treinamento é um pouco menor que para a previsão.

##<a id="appendixa"></a>APÊNDICE A - Guia do RStudio

O RStudio é muito bem documentado, portanto neste apêndice fornecerei alguns links para as seções principais da documentação RStudio para você começar.

1.	Criando projetos

	Você pode organizar e gerenciar seu código R em projetos com o RStudio.  A documentação usando os projetos pode ser encontrada em https://support.rstudio.com/hc/pt-br/articles/200526207-Using-Projects.

	Eu recomendo que você siga estas instruções e crie um projeto para os exemplos de código R neste documento.  

2.	Editar e executar código R

	O RStudio fornece um ambiente integrado para editar e executar código R.  A documentação pode ser encontrada em https://support.rstudio.com/hc/pt-br/articles/200484448-Editing-and-Executing-Code.

3.	Depurando

	O RStudio inclui recursos avançados de depuração.  A documentação para esses recursos estão no https://support.rstudio.com/hc/pt-br/articles/200713843-Debugging-with-RStudio.

	Os recursos de solução de problemas do ponto de interrupção estão em https://support.rstudio.com/hc/pt-br/articles/200534337-Breakpoint-Troubleshooting.

##<a id="appendixb"></a>APÊNDICE B - Leitura adicional

Se você não estiver familiarizado com R, duas introduções estão disponíveis em CRAN.

- R para iniciantes de Emmanuel Paradis é um bom ponto de partida em http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  

- Uma introdução ao R, W. s. Venables et. Al. é um pouco mais complexo, http://cran.r-project.org/doc/manuals/R-intro.html.

Existem muitos livros sobre R que podem ajudá-lo a começar.  Aqui estão alguns que considero úteis:

- "The Art of R Programming; A Tour of Statistical Software Design" de Norman Matloff é uma excelente introdução à programação em R.  

- "R Cookbook" de Paul Teetor fornece uma abordagem de problema e a solução usando R.  

- "R in Action" de Robert Ka
- bacoff é outro livro introdutório bastante útil..  O site "Quick R" é um recurso útil em http://www.statmethods.net/.

- "R Inferno" de Patrick Burns é um livro surpreendentemente bem-humorado que lida com inúmeros tópicos complicados e difíceis que podem ser encontrados ao programar em R. O livro está disponível gratuitamente em http://www.burns-stat.com/documents/books/the-r-inferno/.

- Se você quiser se aprofundar em tópicos avançados em R, dê uma olhada no próximo livro, "Advanced R" de Hadley Wickham.  A versão online deste livro está disponível gratuitamente em http://adv-r.had.co.nz/.

Um catálogo de pacotes de série de tempo de R pode ser encontrado na exibição de tarefa CRAN para análise de série de tempo.: http://cran.r-project.org/web/views/TimeSeries.html. For information on specific time series object packages, you should refer to the documentation for that package.

O livro "Introductory Time Series with R" de Paul Cowpertwait e Andrew Metcalfe dá uma introdução ao uso de R para análise de série de tempo.  Muitos textos mais teóricos fornecem exemplos de R.

<!--Image references-->
[1]: ./media/machine-learning-r-quickstart/fig1.png
[2]: ./media/machine-learning-r-quickstart/fig2.png
[3]: ./media/machine-learning-r-quickstart/fig3.png
[4]: ./media/machine-learning-r-quickstart/fig4.png
[5]: ./media/machine-learning-r-quickstart/fig5.png
[6]: ./media/machine-learning-r-quickstart/fig6.png
[7]: ./media/machine-learning-r-quickstart/fig7.png
[8]: ./media/machine-learning-r-quickstart/fig8.png
[9]: ./media/machine-learning-r-quickstart/fig9.png
[10]: ./media/machine-learning-r-quickstart/fig10.png
[11]: ./media/machine-learning-r-quickstart/fig11.png
[12]: ./media/machine-learning-r-quickstart/fig12.png
[13]: ./media/machine-learning-r-quickstart/fig13.png
[14]: ./media/machine-learning-r-quickstart/fig14.png
[15]: ./media/machine-learning-r-quickstart/fig15.png
[16]: ./media/machine-learning-r-quickstart/fig16.png
[17]: ./media/machine-learning-r-quickstart/fig17.png
[18]: ./media/machine-learning-r-quickstart/fig18.png
[19]: ./media/machine-learning-r-quickstart/fig19.png
[20]: ./media/machine-learning-r-quickstart/fig20.png
[21]: ./media/machine-learning-r-quickstart/fig21.png
[22]: ./media/machine-learning-r-quickstart/fig22.png

[26]: ./media/machine-learning-r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip

<!--HONumber=35.2-->
 