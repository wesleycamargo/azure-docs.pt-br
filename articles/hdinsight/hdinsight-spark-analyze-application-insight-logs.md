---
title: Analisar os logs do Application Insight com Spark no HDInsight | Microsoft Docs
description: Saiba como exportar logs do Application Insight para o armazenamento de blobs e, em seguida, analise os logs com Spark no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 883beae6-9839-45b5-94f7-7eb0f4534ad5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/10/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c07f0da21eab0c90ad9608dfaeb29dd4a01a6b7
ms.openlocfilehash: b3ff0e93ee144e98dec69c0678c3b467db1e0bc0


---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>Analisar logs de telemetria do Application Insights com Spark no HDInsight

[Visual Studio Application Insights](../application-insights/app-insights-overview.md) é um serviço de análise que monitora seus aplicativos Web. Os dados de telemetria gerados pelo Application Insights podem ser exportados para o armazenamento do Azure e, de lá, podem ser analisados pelo HDInsight.

Neste documento, você aprenderá a usar o HDInsight para analisar dados de telemetria do Application Insights usando o Apache Spark.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure.

* Um aplicativo que está configurado para usar o Application Insights. 

* Familiaridade com a criação de um cluster HDInsight baseado em Linux. Se você não estiver familiarizado com a criação de um cluster, veja [Criar Spark no HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) para obter mais informações.
  
  > [!IMPORTANT]
  > As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Um navegador da Web. Isso é usado para executar análise de forma interativa usando um Jupyter Notebook.

Os itens a seguir foram usados em desenvolvimento e testes neste documento:

* Os dados de telemetria do Application Insights foram gerados usando um [aplicativo Web do Node.js configurado para usar o Application Insights](../application-insights/app-insights-nodejs.md).

* Um cluster Spark no HDInsight baseado em Linux versão 3.4 foi usado para analisar os dados.

## <a name="architecture-and-planning"></a>Arquitetura e planejamento
O diagrama a seguir ilustra a arquitetura de serviço deste exemplo:

![diagrama mostrando dados que fluem do Application Insights para o armazenamento de blobs e, em seguida, são processados pelo Spark no HDInsight](./media/hdinsight-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Armazenamento do Azure

Um cluster HDInsight pode acessar diretamente os blobs de bloco de uma conta de armazenamento do Azure, e o Application Insights pode ser configurado para exportar continuamente informações de telemetria para blobs no armazenamento do Azure. No entanto, há alguns requisitos que devem ser seguidos:

* **Local**: a conta de armazenamento deve estar localizada na mesma região do HDInsight. Isso reduz a latência ao acessar os dados e evita encargos de saída que ocorrem quando você move dados entre regiões.
* **Tipo de Blob**: o HDInsight dá suporte apenas aos blobs de bloco. O Application Insights usa blobs de bloco como padrão, então, deve funcionar por padrão com o HDInsight.
* **Permissões de acesso**: se você usar a mesma conta de armazenamento para a exportação contínua do Application Insights e o armazenamento padrão do HDInsight, o HDInsight terá acesso completo aos dados de telemetria do Application Insights. Isso significa que é possível excluir os dados de telemetria do cluster HDInsight.
  
    Em vez disso, é recomendável que você use contas de armazenamento separadas para telemetria do HDInsight e do Application Insights e [use SAS (Assinaturas de Acesso Compartilhado) para restringir o acesso aos dados do HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md). O uso de uma SAS permite que você conceda acesso somente leitura do HDInsight aos dados de telemetria.

### <a name="data-schema"></a>Esquema de dados

O Application Insights fornece informações para [exportar o modelo de dados](../application-insights/app-insights-export-data-model.md) para o formato de dados de telemetria exportados para os blobs. As etapas neste documento usam Spark SQL para trabalhar com os dados. O Spark SQL pode gerar automaticamente um esquema para a estrutura de dados JSON registrada pelo Application Insights. Portanto, você não precisará definir manualmente o esquema ao executar a análise.

## <a name="export-telemetry-data"></a>Exportar dados de telemetria
Siga as etapas em [Configurar a exportação contínua](../application-insights/app-insights-export-telemetry.md) para configurar o Application Insights para exportar informações de telemetria para um blob de armazenamento do Azure.

## <a name="configure-hdinsight-to-access-the-data"></a>Configurar o HDInsight para acessar os dados
Use as informações em [Usar SAS (Assinaturas de Acesso Compartilhado) para restringir o acesso aos dados do HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md) para criar uma SAS para o contêiner de blob que contém os dados de telemetria exportados. A SAS deve fornecer acesso somente leitura aos dados.

O documento de Assinatura de Acesso Compartilhado fornece informações sobre como você pode adicionar o armazenamento SAS a um cluster HDInsight baseado em Linux existente. Ele também fornece informações sobre como adicioná-lo ao criar um novo cluster HDInsight.

## <a name="analyze-the-data-using-python-pyspark"></a>Analisar os dados usando Python (PySpark)
1. No [Portal do Azure](https://portal.azure.com), selecione o Spark no cluster HDInsight. Na seção **Links Rápidos**, selecione **Painéis do Cluster**, em seguida, selecione **Notebook Jupyter** na folha Painel do Cluster__.
   
    ![Os painéis de cluster](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. No canto superior direito da página Jupyter, selecione **Novo**, então, **PySpark**. Isso abre uma nova guia do navegador que contém um Jupyter Notebook com base em Python.
3. No primeiro campo (denominado **célula**) na página, digite o seguinte:
   
        sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   
    Isso permite que o Spark acesse de forma recursiva a estrutura de diretórios para os dados de entrada. A telemetria do Application Insights é registrada em uma estrutura de diretórios semelhante ao seguinte:
   
        /{telemetry type}/YYYY-MM-DD/{##}/
4. Use **SHIFT+ENTER** para executar o código. No lado esquerdo da célula, um '\*' será exibido entre colchetes para indicar que o código nessa célula está sendo executado. Quando for concluído, o '\*' será alterado para um número e uma saída semelhante ao seguinte será exibida abaixo da célula:
   
        Creating SparkContext as 'sc'
   
        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔
   
        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Uma nova célula será criada abaixo da primeira. Digite o seguinte na nova célula. Substitua **CONTAINER** e **STORAGEACCOUNT** pelo nome da conta de armazenamento do Azure e o nome do contêiner de blobs usado ao configurar a exportação contínua do Application Insights.
   
        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   
    Use **SHIFT+ENTER** para executar essa célula. Você verá um resultado semelhante ao seguinte:
   
        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
   
    O caminho wasb retornado é o local dos dados de telemetria do Application Insights. Altere a linha `hdfs dfs -ls` na célula para usar o caminho wasb retornado, em seguida, use **SHIFT+ENTER** para executar a célula novamente. Desta vez, os resultados devem exibir os diretórios que contêm dados de telemetria.
   
   > [!NOTE]
   > Para o restante das etapas desta seção, foi usado o diretório `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests`. Este diretório pode não existir, a menos que os dados de telemetria sejam para um aplicativo Web. Se você estiver usando dados de telemetria que não incluam um diretório de solicitações, escolha outro diretório e ajuste o restante das etapas para usar esse diretório e o esquema para os dados armazenados nele.
   > 
   > 
6. Na próxima célula, digite o seguinte. Substitua **WASB\_PATH** pelo caminho da etapa anterior.
   
        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)
   
    Isso cria um novo dataframe dos arquivos JSON exportados pelo processo de exportação contínua. Use **SHIFT+ENTER** para executar essa célula.
7. Na próxima célula, digite e execute o seguinte para exibir o esquema que o Spark criou para os arquivos JSON:
   
        jsonData.printSchema()
   
    O esquema para cada tipo de telemetria será diferente. A seguir está o esquema gerado para solicitações da Web (dados armazenados no subdiretório `Requests` ):
   
        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. Use o seguinte para registrar o dataframe como uma tabela temporária e executar uma consulta em relação aos dados:
   
        jsonData.registerTempTable("requests")
        sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   
    Essa consulta retornará as informações de cidade para os primeiros 20 registros em que context.location.city não é nulo.
   
   > [!NOTE]
   > A estrutura de contexto está presente em toda a telemetria registrada em log pelo Application Insights. No entanto, o elemento city pode não estar preenchido em seus logs. Use o esquema para identificar outros elementos que você possa consultar e que possam conter dados para seus logs.
   > 
   > 
   
    Essa consulta retornará informações semelhantes às seguintes:
   
        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-using-scala"></a>Analisar os dados usando Scala
1. No [Portal do Azure](https://portal.azure.com), selecione o Spark no cluster HDInsight. Na seção **Links Rápidos**, selecione **Painéis do Cluster**, em seguida, selecione **Notebook Jupyter** na folha Painel do Cluster__.
   
    ![Os painéis de cluster](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. No canto superior direito da página Jupyter, selecione **Novo**, então, **Scala**. Isso abrirá uma nova guia do navegador que contém um Jupyter Notebook com base em Scala.
3. No primeiro campo (denominado **célula**) na página, digite o seguinte:
   
        sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   
    Isso permite que o Spark acesse de forma recursiva a estrutura de diretórios para os dados de entrada. A telemetria do Application Insights é registrada em uma estrutura de diretórios semelhante ao seguinte:
   
        /{telemetry type}/YYYY-MM-DD/{##}/
4. Use **SHIFT+ENTER** para executar o código. No lado esquerdo da célula, um '\*' será exibido entre colchetes para indicar que o código nessa célula está sendo executado. Quando for concluído, o '\*' será alterado para um número e uma saída semelhante ao seguinte será exibida abaixo da célula:
   
        Creating SparkContext as 'sc'
   
        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔
   
        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. Uma nova célula será criada abaixo da primeira. Digite o seguinte na nova célula. Substitua **CONTAINER** e **STORAGEACCOUNT** pelo nome da conta de armazenamento do Azure e o nome do contêiner de blobs usado ao configurar a exportação contínua do Application Insights.
   
        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   
    Use **SHIFT+ENTER** para executar essa célula. Você verá um resultado semelhante ao seguinte:
   
        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
   
    O caminho wasb retornado é o local dos dados de telemetria do Application Insights. Altere a linha `hdfs dfs -ls` na célula para usar o caminho wasb retornado, em seguida, use **SHIFT+ENTER** para executar a célula novamente. Desta vez, os resultados devem exibir os diretórios que contêm dados de telemetria.
   
   > [!NOTE]
   > Para o restante das etapas desta seção, foi usado o diretório `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests`. Este diretório pode não existir, a menos que os dados de telemetria sejam para um aplicativo Web. Se você estiver usando dados de telemetria que não incluam um diretório de solicitações, escolha outro diretório e ajuste o restante das etapas para usar esse diretório e o esquema para os dados armazenados nele.
   > 
   > 
6. Na próxima célula, digite o seguinte. Substitua **WASB\_PATH** pelo caminho da etapa anterior.
   
        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)
   
    Isso cria um novo dataframe dos arquivos JSON exportados pelo processo de exportação contínua. Use **SHIFT+ENTER** para executar essa célula.
7. Na próxima célula, digite e execute o seguinte para exibir o esquema que o Spark criou para os arquivos JSON:
   
        jsonData.printSchema
   
    O esquema para cada tipo de telemetria será diferente. A seguir está o esquema gerado para solicitações da Web (dados armazenados no subdiretório `Requests` ):
   
        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. Use o seguinte para registrar o dataframe como uma tabela temporária e executar uma consulta em relação aos dados:
   
        jsonData.registerTempTable("requests")
        var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   
    Essa consulta retornará as informações de cidade para os primeiros 20 registros em que context.location.city não é nulo.
   
   > [!NOTE]
   > A estrutura de contexto está presente em toda a telemetria registrada em log pelo Application Insights. No entanto, o elemento city pode não estar preenchido em seus logs. Use o esquema para identificar outros elementos que você possa consultar e que possam conter dados para seus logs.
   > 
   > 
   
    Essa consulta retornará informações semelhantes às seguintes:
   
        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Próximas etapas
Para obter mais exemplos de uso do Spark para trabalhar com dados e serviços no Azure, veja os seguintes documentos:

* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

Para saber mais sobre a criação e a execução de aplicativos Spark, veja os seguintes documentos:

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)




<!--HONumber=Jan17_HO3-->


