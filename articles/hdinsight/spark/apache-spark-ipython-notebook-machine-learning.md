---
title: "Criar aplicativos de aprendizado de máquina do Apache Spark no Azure HDInsight | Microsoft Docs"
description: "Instruções passo a passo sobre como criar aplicativos de aprendizado de máquina do Apache Spark em clusters do HDInsight Spark usando o bloco de anotações do Jupyter"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: 2f7dcb9bea05a79a6647b549896c8107f9e830af
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Criar aplicativos de aprendizado de máquina do Apache Spark no Azure HDInsight

Aprenda a criar um aplicativo de aprendizado de máquina do Apache Spark usando um cluster do Spark no HDInsight. Este artigo mostra como usar o bloco de anotações do Jupyter disponível com o cluster para criar e testar esse aplicativo. O aplicativo usa os dados de HVAC.csv de exemplo que estão disponíveis em todos os clusters por padrão.

[MLib](https://spark.apache.org/docs/1.1.0/mllib-guide.html): A biblioteca de Machine Learning escalável do Spark que consiste em algoritmos e utilitários de aprendizado comuns, incluindo classificação, regressão, clustering, filtragem colaborativa, redução de dimensionalidade, bem como primitivos de otimização subjacente.

## <a name="prerequisites"></a>Pré-requisitos:

Você deve ter o seguinte item:

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md). 

## <a name="data"></a>Entender o conjunto de dados

Os dados a seguir mostram a temperatura de destino e a temperatura real de algumas compilações com sistemas de HVAC instalados. A coluna **System** representa a ID do sistema e a coluna **SystemAge** representa o número de anos que o sistema HVAC foi instalado no prédio. Você pode usar os dados neste tutorial para prever se um prédio será mais quente ou frio com base na temperatura de destino, uma ID de sistema e a idade do sistema.

![Exemplo de instantâneo dos dados usados para aprendizado de máquina do Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Exemplo de instantâneo dos dados usados para aprendizado de máquina do Spark")

O arquivo de dados, **HVAC.csv**, está localizado em **\HdiSamples\HdiSamples\SensorSampleData\hvac** em todos os clusters do HDInsight.

## <a name="app"></a>Escrever um aplicativo de aprendizado de máquina do Spark usando o MLlib Spark
Neste aplicativo, você usa um [pipeline ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) do Spark para executar uma classificação de documento. Pipelines ML fornece um conjunto uniforme de APIs de alto nível criado com base em quadros de dados que ajudam os usuários a criar e ajustar os pipelines de aprendizado de máquina práticos. No pipeline, você divide o documento em palavras, converte as palavras em um vetor de recurso numérico e, finalmente, cria um modelo de previsão usando as etiquetas e vetores de recurso. Execute as seguintes etapas para criar o aplicativo.

1. Crie um bloco de notas do Jupyter usando o kernel PySpark. Para obter instruções, consulte [Criar um bloco de notas do Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).
2. Importe os tipos obrigatórios necessários para este cenário. Cole o trecho a seguir em uma célula vazia e, em seguida, pressione **SHIFT + ENTER**. 

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```
3. Carregue os dados (hvac.csv), analisá-los e usá-los para treinar o modelo. 

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0        

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    No trecho de código, você definirá uma função que compare a temperatura real com a temperatura de destino. Se a temperatura real é maior, o prédio está quente, indicado pelo valor **1,0**. Caso contrário, a compilação fica fria, como indicado pelo valor **0,0**. 

4. Configure o pipeline de aprendizado de máquina Spark que consiste de três estágios: tokenizer, hashingTF e lr. 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Para obter mais informações sobre o que é um pipeline e como ele funciona, confira <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Pipeline de aprendizado de máquina Spark</a>.

5. Ajuste o pipeline para o documento de treinamento.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Verifique o documento de treinamento para o ponto de verificação de seu progresso com o aplicativo.
   
    ```PySpark
    training.show()
    ```
   
    Isso deve fornecer um resultado semelhante ao seguinte:

    ```
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Comparando a saída em relação ao arquivo CSV bruto. Por exemplo, a primeira linha do arquivo CSV tem esses dados:

    ![Exemplo de instantâneo dos dados de saída para aprendizado de máquina do Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Exemplo de instantâneo dos dados de saída para aprendizado de máquina do Spark")

    Observe como a temperatura real é menor que a temperatura de destino sugerindo que o prédio está frio. Portanto, no resultado do treinamento, o valor para o **rótulo** na primeira linha é **0,0**, o que significa que o prédio não está quente.

7. Prepare um conjunto de dados para executar o modelo treinado. Para fazer isso, você passa por uma ID de sistema e idade de sistema (denotado como **SystemInfo** no resultado de treinamento), e o modelo prevê se o prédio com essa ID de sistema e idade de sistema será mais quente (indicado por 1,0) ou mais frio (indicado pelo 0,0).
   
    ```PySpark   
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF() 
    ```
8. Por fim, faça as previsões nos dados de teste. 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Você deverá ver um resultado semelhante ao seguinte:

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   Na primeira linha na previsão, você pode ver que para um sistema HVAC com ID 20 e sistema de 25 anos, o prédio está quente (**previsão = 1,0**). O primeiro valor de DenseVector (0,49999) corresponde à previsão 0,0 e o segundo valor (0,5001) corresponde à previsão 1,0. Na saída, mesmo que o segundo valor seja apenas um pouco mais alto, o modelo mostra **previsão = 1,0**.
10. Feche o bloco de anotações para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**. Isso desligará e fechará o bloco de anotações.

## <a name="anaconda"></a>Use a biblioteca Anaconda scikit-learn para aprendizado de máquina do Spark
Os clusters Apache Spark no HDInsight incluem bibliotecas Anaconda. Isso também inclui a biblioteca **scikit-learn** para aprendizado de máquina. A biblioteca também inclui vários conjuntos de dados que você pode usar para criar aplicativos de exemplo diretamente de um bloco de anotações do Jupyter. Para obter exemplos sobre como usar a biblioteca scikit-learn, confira [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]:../hadoop/apache-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]:../hadoop/apache-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
