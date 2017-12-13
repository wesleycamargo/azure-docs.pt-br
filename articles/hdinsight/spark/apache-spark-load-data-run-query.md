---
title: Executar consultas interativas em um cluster HDInsight Spark do Azure | Microsoft Docs
description: "Guia de início rápido sobre como criar um cluster do Apache Spark no HDInsight."
keywords: "início rápido do spark,spark interativo,consulta interativa,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 78ab44a7afa6523e1e9e4082b3f45b1a28affe77
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>Executar consultas interativas em clusters do Spark no HDInsight

Saiba como usar o Notebook Jupyter para executar consultas SQL interativas do Spark em um cluster do Spark. 

O [Notebook Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) é um aplicativo baseado em navegador que estende para a Web a experiência interativa baseada em console. O Spark no HDInsight também inclui o [Notebook Zeppelin](apache-spark-zeppelin-notebook.md). Neste tutorial, é usado o Notebook Jupyter.

Os Notebooks Jupyter nos clusters do HDInsight dão suporte a três kernels – **PySpark**, **PySpark3** e **Spark**. Neste tutorial, é usado o kernel **PySpark**. Para saber mais sobre os três kernels e os benefícios de usar o **PySpark**, consulte [Usar kernels de blocos de anotações do Jupyter com clusters do Apache Spark no HDInsight](apache-spark-jupyter-notebook-kernels.md). Para usar o Notebook Zeppelin, consulte [Usar notebooks Zeppelin com cluster Apache Spark no Azure HDInsight](./apache-spark-zeppelin-notebook.md).

Neste tutorial, você consultará dados em um arquivo CSV. Primeiro você deve carregar esses dados no Spark como um dataframe. Em seguida, você poderá executar consultas no dataframe usando o Notebook Jupyter. 

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster HDInsight Spark do Azure**. Para obter instruções, consulte [Criar um cluster do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Um notebook Jupyter usando PySpark**. Para obter instruções, consulte [Criar um Notebook Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

## <a name="create-a-dataframe-from-a-csv-file"></a>Criar um dataframe usando um arquivo CSV

Com um SQLContext, os aplicativos podem criar dataframes usando um RDD, uma tabela do Hive ou uma fonte de dados existente. 

**Para criar um dataframe usando um arquivo CSV**

1. Crie um notebook Jupyter usando o PySpark, se você ainda não tiver um. Para obter instruções, consulte [Criar um Notebook Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Cole o código a seguir em uma célula vazia do notebook e, em seguida, pressione **SHIFT + ENTER** para executar o código. O código importa os tipos obrigatórios necessários para este cenário:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    Usando o kernel PySpark para criar um notebook, os contextos do Spark e do Hive serão criados automaticamente quando você executar a primeira célula de código. Você não precisa criar nenhum contexto explicitamente.

    Ao executar uma consulta interativa no Jupyter, a legenda da guia ou da janela do navegador da Web mostra um status **(Ocupado)** ao lado do título do notebook. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito. Após a conclusão do trabalho, isso será alterado para um círculo vazio.

    ![Status de consulta interativa SQL Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status de consulta interativa SQL Spark")

3. Execute o seguinte código para criar um dataframe e uma tabela temporária (**hvac**): o código não extrai todas as colunas disponíveis no arquivo CSV. 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for our data
    Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
    
    # Parse the data and create a schema
    hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
    hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
    
    # Infer the schema and create a table       
    hvacTable = sqlContext.createDataFrame(hvac)
    hvacTable.registerTempTable('hvactemptable')
    dfw = DataFrameWriter(hvacTable)
    dfw.saveAsTable('hvac')
    ```
    A captura de tela a seguir mostra um instantâneo do arquivo HVAC.csv. O arquivo CSV vem com todos os clusters do Spark no HDInsigt. Os dados capturam as variações de temperatura de um prédio.

    ![Instantâneo de dados para consulta SQL interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Instantâneo de dados para consulta SQL interativa do Spark")

## <a name="run-queries-on-the-dataframe"></a>Executar consultas no dataframe

Depois que a tabela for criada, você poderá executar uma consulta interativa nos dados.

**Para executar uma consulta**

1. Execute o seguinte código em uma célula vazia do notebook:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Como o kernel PySpark é usado no notebook, agora é possível executar uma consulta SQL interativa diretamente na tabela temporária **hvac** que você criou usando a mágica `%%sql`. Para saber mais sobre a palavra mágica `%%sql`, e outras palavras mágicas disponíveis com o kernel PySpark, confira [Kernels disponíveis em notebooks Jupyter com clusters HDInsight Spark](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   A saída tabular a seguir é exibida por padrão.

     ![Tabela de saída do resultado da consulta interativa Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabela de saída do resultado da consulta interativa Spark")

3. Você também pode ver os resultados em outras visualizações. Para ver um gráfico de área para a mesma saída, selecione **Área** e, em seguida, defina outros valores conforme mostrado.

    ![Grafo de área de resultado de consulta interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Grafo de área de resultado de consulta interativa do Spark")

10. No menu **Arquivo** do bloco de anotações, clique em **Salvar e Criar Ponto de Verificação**. 

11. Se você estiver começando o [próximo tutorial](apache-spark-use-bi-tools.md) agora, deixe o bloco de anotações aberto. Se não estiver, desligue o bloco de anotações para liberar os recursos de cluster: no menu **Arquivo** no bloco de anotações, clique em **Fechar e Interromper**.

## <a name="next-step"></a>Próxima etapa

Neste artigo, você aprendeu como executar consultas interativas no Spark Notebooks Jupyter. Avance para o próximo artigo para ver como os dados que você registrou no Spark podem ser removidos em uma ferramenta de análise de BI, assim como Power BI e Tableau. 

> [!div class="nextstepaction"]
>[Spark BI usando ferramentas de visualização de dados com o Azure HDInsight](apache-spark-use-bi-tools.md)




