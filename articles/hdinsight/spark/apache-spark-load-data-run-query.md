---
title: 'Tutorial: Carregar dados e executar consultas em um cluster do Apache Spark no Azure HDInsight | Microsoft Docs'
description: Aprenda a carregar dados e executar consultas interativas em clusters do Spark no Azure HDInsight.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.author: jgao
ms.date: 05/07/2018
ms.openlocfilehash: 63a876dc148129cd2a3eb93ed7ab6baf06a07c62
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Tutorial: Carregar dados e executar consultas em um cluster do Apache Spark no Azure HDInsight

Neste tutorial, você aprenderá a usar criar um dataframe de um arquivo csv e como executar consultas de SQL Spark interativas em um cluster do Apache Spark no Azure HDInsight. No Spark, um dataframe é uma coleção distribuída de dados organizados em colunas nomeadas. O dataframe é conceitualmente equivalente a uma tabela em um banco de dados relacional ou uma estrutura de dados em R/Python.
 
Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um dataframe usando um arquivo CSV
> * Executar consultas no dataframe

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

* Concluir [Criar um cluster do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Criar um dataframe usando um arquivo CSV

Os aplicativos podem criar dataframes de um Conjunto de Dados Distribuídos Resiliente existente, uma tabela do Hive ou uma fonte de dados usando o objeto SQLContext. A captura de tela a seguir mostra um instantâneo do arquivo HVAC.csv usado neste tutorial. O arquivo CSV vem com todos os clusters do Spark no HDInsight. Os dados capturam as variações de temperatura de algumas construções.
    
![Instantâneo de dados para consulta SQL interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Instantâneo de dados para consulta SQL interativa do Spark")


1. Abra o bloco de anotações do Jupyter que você criou na seção pré-requisitos.
2. Cole o código a seguir em uma célula vazia do notebook e, em seguida, pressione **SHIFT + ENTER** para executar o código. O código importa os tipos obrigatórios necessários para este cenário:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Ao executar uma consulta interativa no Jupyter, a legenda da guia ou da janela do navegador da Web mostra um status **(Ocupado)** ao lado do título do notebook. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito. Após a conclusão do trabalho, isso será alterado para um círculo vazio.

    ![Status de consulta interativa SQL Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status de consulta interativa SQL Spark")

3. Execute o seguinte código para criar um dataframe e uma tabela temporária (**hvac**) executando o código a seguir. O código não extrai todas as colunas disponíveis no arquivo CSV. 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for the data
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

    > [!NOTE]
    > Usando o kernel PySpark para criar um notebook, os contextos SQL são criados automaticamente quando você executar a primeira célula de código. Você não precisa criar nenhum contexto explicitamente.


## <a name="run-queries-on-the-dataframe"></a>Executar consultas no dataframe

Depois que a tabela for criada, você poderá executar uma consulta interativa nos dados.

1. Execute o seguinte código em uma célula vazia do notebook:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Como o kernel PySpark é usado no notebook, agora é possível executar uma consulta SQL interativa diretamente na tabela temporária **hvac**.

   A saída tabular a seguir é exibida.

     ![Tabela de saída do resultado da consulta interativa Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabela de saída do resultado da consulta interativa Spark")

3. Você também pode ver os resultados em outras visualizações. Para ver um gráfico de área para a mesma saída, selecione **Área** e, em seguida, defina outros valores conforme mostrado.

    ![Grafo de área de resultado de consulta interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Grafo de área de resultado de consulta interativa do Spark")

10. No menu **Arquivo** do bloco de anotações, selecione **Salvar e Ponto de Verificação**. 

11. Se você estiver começando o [próximo tutorial](apache-spark-use-bi-tools.md) agora, deixe o bloco de anotações aberto. Se não estiver, desligue o bloco de anotações para liberar os recursos de cluster: no menu **Arquivo** no bloco de anotações, selecione **Fechar e Interromper**.

## <a name="clean-up-resources"></a>Limpar recursos

Com o HDInsight, seus dados são armazenados no Armazenamento do Azure ou no Azure Data Lake Store, assim você poderá excluir um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Se você planeja trabalhar no próximo tutorial imediatamente, convém manter o cluster.

Abra o cluster no portal do Azure e selecione **Excluir**.

![Excluir o cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Excluir o cluster HDInsight")

Também é possível selecionar o nome do grupo de recursos para abrir a página do grupo de recursos, e depois selecionar **Excluir grupo de recursos**. Ao excluir o grupo de recursos, você exclui o cluster Spark do HDInsight e a conta de armazenamento padrão.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

* Crie um dataframe Spark.
* Execute Spark SQL no dataframe.

Avance para o próximo artigo para ver como os dados que você registrou no Spark podem ser removidos em uma ferramenta de análise de BI, assim como Power BI. 
> [!div class="nextstepaction"]
> [Analisar dados usando ferramentas de BI](apache-spark-use-bi-tools.md)

