<properties title="Azure Machine Learning Sample: Credit risk prediction" pageTitle="Machine Learning Sample: Credit risk prediction | Azure" description="A sample Azure Machine Learning experiment to develop a binary classification model that predicts if an applicant is a low credit risk or a high credit risk." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Amostra de Aprendizado de Máquina do Azure: Previsão de risco de crédito

*Você pode encontrar o teste de amostra associado a esse modelo no Estúdio AM na seção <b>TESTES</b> sob a guia <b>AMOSTRAS</b>. O nome do teste é:*

    Sample Experiment - German Credit - Development

*Para obter um acompanhamento detalhado de como criar e usar uma versão simplificada desse teste, consulte [Desenvolver uma solução preditiva com o Aprendizado de Máquina do Azure][Desenvolver uma solução preditiva com o Aprendizado de Máquina do Azure].*

A finalidade desse teste é prever o risco de crédito com base nas informações fornecidas em um aplicativo de crédito. A previsão é um valor binário: baixo risco ou alto risco.

<!-- Removed until the Training and Scoring parts are fixed This example is divided into 3 sample experiments:  - Development Experiment – for experimenting with different models - Training Experiment – to train the one chosen model - Scoring Experiment – to set up a web service using the trained model -->

## Descrição do conjunto de dados

O teste usa o conjunto de dados UCI Statlog (Dados de Crédito Alemão) que pode ser encontrado aqui:
<http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)>.
Estamos usando o arquivo german.data desse site.

O conjunto de dados classifica pessoas, descritas como um conjunto de atributos, como baixos ou altos riscos de crédito. Cada exemplo representa uma pessoa. Há 20 recursos, ambos numéricos e categóricos, e um rótulo binário (o valor de risco de crédito). As entradas de alto risco de crédito têm rótulo = 2, as entradas com baixo risco de crédito têm rótulo = 1. O custo de classificar incorretamente um exemplo de baixo risco como alto é 1, ao passo que o custo de classificar incorretamente um exemplo de alto risco como baixo é 5.

## Teste de desenvolvimento

O conjunto de dados original tem um formato separado por espaços em branco. Transformamos o conjunto de dados em formato CSV e o carregamos no Estúdio AM. Essa transformação pode ser feita usando o Powershell:

    cat dataset.txt | %{$_ -replace " ",","} | sc german.csv

Ou usando o comando Unix sed:

    sed 's/ /,/g' german.data > german.csv

Começamos usando o **Editor de Metadados** para adicionar nomes de coluna para substituir os nomes de coluna padrão do conjunto de dados por nomes mais significativos que foram tirados da descrição do conjunto de dados no site UCI. Os novos nomes de coluna são separados por vírgulas no campo **Novo nome de coluna** do **Editor de metadados**.

Em seguida, geramos conjuntos de treinamento e teste que serão usados para desenvolver o modelo de previsão de risco. Dividimos o conjunto de dados original em conjuntos de treinamento e teste do mesmo tamanho, usando o módulo **Dividir** com a **Taxa das primeiras linhas de saída para entrada** definida para 0,5.

Como o custo de uma classificação incorreta de exemplo de alto risco como baixo é 5 vezes maior do que o custo de classificação incorreta de baixo risco como alto, geramos um novo conjunto de dados que reflete essa função de custo. No novo conjunto de dados, cada exemplo de alto risco é replicado 5 vezes, ao passo que os exemplos de baixo risco são mantidos como estão. A divisão de conjuntos de dados de treinamento e de teste é feita antes dessa replicação para evitar que o mesmo exemplo esteja em ambos, nos conjuntos de treinamento e de teste.

Essa replicação é feita por meio do seguinte código R que é executado usando o módulo **Executar script R**:

    dataset1 <- maml.mapInputPort(1)
    data.set<-dataset1[dataset1[,21]==1,]
    pos<-dataset1[dataset1[,21]==2,]
    for (i in 1:5) data.set<-rbind(data.set,pos)
    maml.mapOutputPort("data.set")

Em nosso teste, comparamos duas abordagens para gerar modelos: treinamento sobre o conjunto de dados original e treinamento sobre o conjunto de dados replicado. Nas duas abordagens, alinhadas com a função de custo do problema, testamos sobre o conjunto de teste com replicação. O fluxo de trabalho final para a divisão e a replicação está representado abaixo. Neste fluxo de trabalho, a saída à esquerda do módulo **Dividir** é um conjunto de treinamento e a saída à direita é um conjunto de teste. Observe que o conjunto de treinamento é usado posteriormente com e sem a opção **Executar script R** - ou seja, com e sem replicação.

![Divisão de dados de treinamento e teste][Divisão de dados de treinamento e teste]

Além de verificar o efeito de replicação de exemplos no conjunto de treinamento, também comparamos o desempenho dos dois algoritmos: Máquina de vector de suporte (SVM) e árvore de decisão aumentada. Dessa maneira, geramos 4 modelos de forma eficiente:

-   SVM, treinado com dados originais
-   SVM, treinado com dados replicados
-   Árvore de decisão aumentada, treinada com dados originais
-   Árvore de decisão aumentada, treinada com dados replicados

Árvores de decisão aumentadas funcionam bem com recursos de qualquer tipo. Entretanto, como o módulo SVM gera um classificador linear, o modelo que ele gera apresenta o melhor erro de teste quando todos os recursos têm a mesma escala. Para converter todos os recursos para a mesma escala, usamos o módulo **Transformar dados por escala** com uma transformação tanh. Essa transformação transforma todos os recursos numéricos em intervalo [0,1]. Observe que os recursos de cadeia de caracteres são convertidos pelo módulo SVM em recursos categóricos e depois em recursos binários 0/1; portanto, não precisamos transformar manualmente os recursos de cadeia de caracteres.

Inicializamos o algoritmo de aprendizado usando o módulo **Máquina de vetor de suporte de duas classes** ou o módulo **Árvore de decisão aumentada de duas classes** e depois usamos o módulo **Modelo de treinamento I** para criar o modelo real. Esses modelos são usados pelos módulos do **Modelo de Pontuação** para produzir pontuações de exemplos de teste. Um fluxo de trabalho de exemplo que combina esses módulos e utiliza SVM e o conjunto de treinamento replicado é ilustrado abaixo. Observe que o **Modelo de treinamento** é conectado ao conjunto de treinamento, ao passo que o **Modelo de Pontuação** é conectado ao conjunto de teste.

![Treinando e pontuando um modelo][Treinando e pontuando um modelo]

No estágio de avaliação do teste, calculamos a precisão de cada um dos 4 modelos acima. Para essa finalidade, usamos o módulo **Avaliar modelo**. Observe que esse módulo calcula somente a precisão quando todos os exemplos possuem o mesmo custo de classificação incorreta. Mas, como anteriormente replicamos exemplos positivos, a precisão calculada por **Avaliar modelo** é sensível aos custos e é

![Cálculo de precisão][Cálculo de precisão]

onde *n+* e *n-* são os números de exemplos positivo e negativo no conjunto de dados original, e *e+* e *e-* são os números de exemplos positivo e negativo calculados incorretamente no conjunto de dados original.

O módulo **Avaliar modelo** compara 2 modelos pontuados, assim, usamos um módulo **Avaliar modelo** para comparar os 2 modelos SVM, e um para comparar os 2 modelos de árvore de decisão aumentada. Combinamos tudo isso em uma tabela para exibir todos os 4 resultados. **Avaliar modelo** produz uma tabela com uma única linha que contém várias métricas. Usamos o módulo **Adicionar linhas** para combinar todos os resultados em uma única tabela. Em seguida, anotamos a tabela com as precisões dos 4 módulos usando um script R no módulo **Executar script R**, onde inserimos manualmente os nomes das linhas da tabela final. Finalmente, removemos as colunas com métricas não relevantes usando o módulo **Projetar colunas**.

Os resultados finais do teste, obtidos clicando com o botão direito do mouse na saída **Conjuntos de dados de resultados** de **Projetar colunas** são:

![Resultados][Resultados]

onde a primeira coluna é o nome do algoritmo de aprendizado de máquina usado para gerar um modelo, a segunda coluna indica o tipo de conjunto de treinamento e a terceira coluna é a precisão sensível aos custos. Nesse teste, o modelo SVM, que funciona com o conjunto de dados de treinamento replicado, fornece a melhor precisão.

<!-- Removed until the Training and Scoring parts are fixed ##Training Experiment  The sample training experiment is a simplified version of the larger experiment using just the chosen SVM training model.   Notice that unlike the development experiment, in the training experiment we chose to load the dataset from Azure blob storage using the **Reader** module. Having the dataset stored in Azure is very common when it is generated by other programs. By reading the dataset directly from Azure we skip the step of manually uploading the dataset into ML Studio. The parameters of the **Reader** module are shown below. In this example, the storage account name is “datascience” and the dataset file “german.csv” is placed in container “sampleexperiments”. The account key is an access key of an Azure storage account. This key can be retrieved from your account in the Azure management portal.  ![Azure storage parameters][screen3]   ##Scoring Experiment  The purpose of the sample scoring experiment is to set up a REST API web service that will score test examples. The trained model in this experiment (“Credit Risk model”) was created from the training experiment by right-clicking the Train Model module and selecting **Save as Trained Model**. In this scoring experiment we load test examples, normalize them, and perform scoring using this saved trained model.   After running this experiment and verifying that it generates the right scores we prepare to publish it as a web service by defining the service input and output. We define the web service input as the input port to the **Transform Data By Scaling** module by right-clicking the port and selecting **Set as Publish Input**. The web service output is set to the output of the **Score Model** module by right-clicking the output of **Score Model** and selecting **Set as Publish Output**.   After setting up the service input and output we need to rerun the experiment and then click **Publish Web Service**. This publishes the web service to the staging environment and takes us to the ML Studio **WEB SERVICES** page. Here we can configure and test the service with sample data.  When the service is ready to go live, go to the **CONFIGURATION** tab on the **WEB SERVICES** page and click **READY FOR PRODUCTION?**. A request will be sent to the IT administrator for Machine Learning who can promote the service to the production environment.  ![Web service ready for production][screen4]  -->

  [Desenvolver uma solução preditiva com o Aprendizado de Máquina do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/
  [Divisão de dados de treinamento e teste]: ./media/machine-learning-sample-credit-risk-prediction/screen1.jpg
  [Treinando e pontuando um modelo]: ./media/machine-learning-sample-credit-risk-prediction/screen2.jpg
  [Cálculo de precisão]: ./media/machine-learning-sample-credit-risk-prediction/formula.jpg
  [Resultados]: ./media/machine-learning-sample-credit-risk-prediction/results.jpg
