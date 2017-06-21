---
title: "Introdução - Apache Spark e consultas interativas do Spark SQL - Azure HDInsight | Microsoft Docs"
description: "Guia de início rápido sobre como criar um cluster do Apache Spark no HDInsight e executar consultas interativas do HDInsight Spark usando blocos de anotações Jupyter."
keywords: "início rápido do spark,spark interativo,consulta interativa,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/25/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 925dbf5e595941da58e7d705175d0cc63bbf6a16
ms.contentlocale: pt-br
ms.lasthandoff: 06/07/2017


---
# <a name="get-started-create-an-apache-spark-cluster-in-hdinsight-and-run-interactive-spark-sql-queries"></a>Introdução: criar um cluster do Apache Spark no HDInsight e executar consultas interativas do Spark SQL

Saiba como criar um cluster [Apache Spark](hdinsight-apache-spark-overview.md) no HDInsight e executar consultas interativas SQL Spark usando um bloco de anotações [Jupyter](https://jupyter.org).

   ![Diagrama de início rápido que descreve as etapas para criar um cluster Apache Spark no Azure HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Início rápido do Spark usando o Apache Spark no HDInsight. Etapas ilustradas: criar um cluster; executar consulta interativa do Spark")

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Antes de começar este tutorial, você deverá ter uma assinatura do Azure. Consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Criar cluster do Azure HDInsight Spark

Nesta seção, você criará um cluster Spark do HDInsight usando um [modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Para obter outros métodos de criação de cluster, confira [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem a seguir para abrir o modelo no portal do Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Insira os valores a seguir:

    ![Criar um cluster Spark do HDInsight usando um modelo do Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Criar um cluster Spark no HDInsight usando um modelo do Azure Resource Manager")

    * **Assinatura**: selecione sua assinatura do Azure para esse cluster.
    * No **Grupo de recursos**: crie um grupo de recursos ou selecione um existente. O grupo de recursos é usado para gerenciar recursos do Azure em seus projetos.
    * **Local**: selecione um local para o grupo de recursos.  Este local também é usado para o armazenamento de clusters padrão e do cluster HDInsight.
    * **ClusterName**: insira um nome para o cluster Hadoop que você criará.
    * **Versão do Spark**: selecione a versão do Spark que você deseja instalar no cluster.
    * **Nome e senha de logon do cluster**: o nome de logon padrão é admin.
    * **Nome de usuário e senha de SSH**.

   Anote esses valores.  Você precisará deles mais tarde no tutorial.

3. Selecione **Concordo com os termos e condições declarados acima**, selecione **Fixar no painel**e, em seguida, clique em **Comprar**. Você poderá ver um novo bloco intitulado Como enviar a implantação para a implantação do modelo. Demora cerca de 20 minutos para criar o cluster.

> [!NOTE]
> Este artigo cria um cluster Spark que usa [Blobs do Armazenamento do Azure como o armazenamento de cluster](hdinsight-hadoop-use-blob-storage.md). Você também pode criar um cluster Spark que usa o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) como um armazenamento adicional, além de Blobs do Armazenamento do Azure como o armazenamento padrão. Para obter instruções, confira [Criar um cluster HDInsight com o Repositório Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-an-interactive-spark-sql-query"></a>Executar uma consulta interativa SQL Spark

Nesta seção, você pode usar o bloto de anotações Jupyter para executar consultas interativas do Spark SQL em relação ao cluster Spark que criou anteriormente. Os clusters HDInsight Spark fornecem três kernels que você pode usar com o bloco de anotações do Jupyter. Estes são:

* **PySpark** (para aplicativos escritos em Python)
* **PySpark3** (para aplicativos escritos em Python 3)
* **Spark** (para aplicativos escritos em Scala)

Neste artigo, você deve usar o kernel **PySpark** no bloco de anotações de onde executa a consulta SQL interativa do Spark. Para saber mais sobre os três kernels, consulte [clusters kernels de blocos de anotações do Jupyter de uso com o Apache Spark no HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md). Alguns dos principais benefícios de usar o kernel PySpark são:

* Os contextos de Spark e Hive são definidos automaticamente.
* Use diferentes mágicas de célula, como `%%sql`, para executar diretamente consultas interativas SQL ou do Hive, sem nenhum trecho de código anterior.
* A saída de consultas interativas é visualizada automaticamente.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Criar um bloco de anotações do Jupyter com o kernel PySpark

1. Abra o [Portal do Azure](https://portal.azure.com/).

2. Se você tiver optado por fixar o cluster ao painel, clique no bloco do cluster no painel para iniciar a folha de cluster.

    Se você não fixar o cluster ao painel, no painel esquerdo, clique em **Clusters HDInsight** e clique no cluster que você criou.

3. Em **Links rápidos**, clique em **Painéis de cluster**e, em seguida, clique em **Anotações do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

   ![Abrir um bloco de anotações Jupyter para executar uma consulta interativa SQL do Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Abrir um bloco de anotações Jupyter para executar uma consulta interativa SQL do Spark")

   > [!NOTE]
   > Você também pode acessar o bloco de anotações Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Crie um notebook. Clique em **Novo** e em **PySpark**.

   ![Criar um bloco de anotações Jupyter para executar consulta interativa SQL do Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Criar um bloco de anotações Jupyter para executar uma consulta interativa SQL do Spark")

   Um novo bloco de anotações é criado e aberto com o nome Untitled(Untitled.pynb).

4. Clique no nome do bloco de anotações na parte superior e, se desejar, digite um nome amigável.

    ![Forneça um nome para o bloco de anotações Jupyter para executar a consulta interativa Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Forneça um nome para o bloco de anotações Jupyter para executar a consulta interativa Spark")

5. Cole o código a seguir em uma célula vazia e pressione **SHIFT + ENTER** para executar o código. O código importa os tipos obrigatórios necessários para este cenário:

        from pyspark.sql.types import *

    Por ter criado um notebook usando o kernel PySpark, não será necessário criar nenhum contexto explicitamente. Os contextos do Spark e do Hive são criados automaticamente para você ao executar a primeira célula do código.

    ![Status de consulta interativa SQL Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-interactive-spark-query-status.png "Status de consulta interativa SQL Spark")

    Toda vez que você executar uma consulta interativa no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** com o título do bloco de anotações. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito. Após a conclusão do trabalho, isso será alterado para um círculo vazio.

6. Registre um conjunto de dados de exemplo como uma tabela temporária (**hvac**) executando o código a seguir.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

        # Register the data frame as a table to run queries against
        hvacdf.registerTempTable("hvac")

    Os clusters Spark no HDInsight são fornecidos com um arquivo de dados de exemplo, **hvac.csv**, em **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

7. Para executar a consulta interativa em relação aos dados, use o código a seguir.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Como está usando um kernel PySpark, agora você pode executar diretamente uma consulta interativa SQL na tabela temporária **hvac** que você criou usando a palavra mágica `%%sql`. Para saber mais sobre a palavra mágica `%%sql`, e outras palavras mágicas disponíveis com o kernel PySpark, confira [Kernels disponíveis em notebooks Jupyter com clusters HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   A saída tabular a seguir é exibida por padrão.

     ![Tabela de saída do resultado da consulta interativa Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-interactive-spark-query-result.png "Tabela de saída do resultado da consulta interativa Spark")

    Você também pode ver os resultados em outras visualizações. Por exemplo, um gráfico de área para a mesma saída seria semelhante ao seguinte.

    ![Gráfico de área de resultado de consulta interativa do Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-interactive-spark-query-result-area-chart.png "Gráfico de área de resultado de consulta interativa do Spark")

9. Depois de concluir a execução do aplicativo, encerre o notebook para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**.

## <a name="delete-the-cluster"></a>Excluir o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot-access-control"></a>Solucionar problemas de controle de acesso

Se você tiver um problema com a criação de clusters HDInsight, consulte [requisitos de controle de acesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="see-also"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Análise da telemetria de dados do Application Insight usando o Spark no HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: storage-create-storage-account.md

