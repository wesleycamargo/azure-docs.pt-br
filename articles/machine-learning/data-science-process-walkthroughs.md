---
title: "Instruções passo a passo do Processo de Ciência de Dados de Equipe | Microsoft Docs"
description: "O passo a passo mostra como combinar ferramentas e serviços de nuvem e locais em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aa63d5a5-25ee-4c4b-9a4c-7553b98d7f6e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: c0f0ff599909234b2b43f2b8512d77f9053b8a09
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---
# <a name="team-data-science-process-walkthroughs"></a>Passo a passo do Processo de Ciência de Dados de Equipe
Os **passo a passos de ponta a ponta** detalhados aqui demonstram as etapas do Processo de Ciência de Dados de Equipe para **cenários específicos**. Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um **aplicativo inteligente**. As instruções passo a passo são agrupadas por **plataforma** utilizada: 

- Spark com PySpark e Scala
- HDInsight (Hadoop)
- Azure Data Lake 
- SQL Server
- SQL Data Warehouse 


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>HDInsight Spark usando o PySpark e o Scala

- O passo a passo [Utilizar Spark no Azure HDInsight](machine-learning-data-science-spark-overview.md) usa o Processo de Ciência de Dados de Equipe em um cenário que usa um [cluster Spark do Azure HDInsight ](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e apresentar dados de engenharia do conjunto de dados publicamente disponível de corridas de táxi e tarifas em NYC.

- O passo a passo [Utilizar o Scala com o Spark no Azure](machine-learning-data-science-process-scala-walkthrough.md) mostra como usar o Scala para tarefas de Machine Learning supervisionado com a biblioteca de Machine Learning do Spark (MLlib) e pacotes SparkML em um cluster Spark do Azure HDInsight. Ele explica as tarefas que constituem o [Processo de ciência de dados](http://aka.ms/datascienceprocess): ingestão e exploração de dados, visualização, engenharia de recursos, modelagem e consumo de modelo. Os modelos criados incluem a regressão logística e linear, florestas aleatórias e árvores aumentadas gradientes.


## <a name="hdinsight-hadoop"></a>HDInsight Hadoop 

- O passo a passo [Utilizar clusters Hadoop do HDInsight](machine-learning-data-science-process-hive-walkthrough.md) usa um [cluster Hadoop do Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e apresentar dados de engenharia de um conjunto de dados publicamente disponível de corridas de táxi e tarifas em NYC

- O passo a passo [Utilizar clusters Hadoop do Azure HDInsight em um conjunto de dados de 1 TB](machine-learning-data-science-process-hive-criteo-walkthrough.md) apresenta um cenário que usa um [cluster Hadoop do Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e apresentar dados de engenharia, bem como reduzir dados de um conjuntos de dados da [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) publicamente disponível.


## <a name="azure-data-lake"></a>Azure Data Lake

- O passo a passo [Utilizar o Azure Data Lake para ciência de dados](machine-learning-data-science-process-data-lake-walkthrough.md) mostra como usar o Azure Data Lake para tarefas de exploração de dados e classificação binária em um exemplo do conjunto de dados de táxis de NYC para prever se uma corrida será paga ou não por um cliente. 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server e SQL Data Warehouse 

- O passo a passo [Utilizar o SQL Data Warehouse](machine-learning-data-science-process-sqldw-walkthrough.md) mostra como compilar e implantar modelos de regressão e classificação de Machine Learning usando o SQL DW (SQL Data Warehouse) em um conjunto de dados publicamente disponível de corridas de táxi e tarifas em NYC.

- O passo a passo [Utilizar o SQL Server](machine-learning-data-science-process-sql-walkthrough.md) mostra como compilar e implantar modelos de regressão e classificação de Machine Learning usando o SQL Server e um conjunto de dados publicamente disponível de corridas de táxi e tarifas em NYC.

- O passo a passo [Utilizar os Serviços de R do SQL Server](https://msdn.microsoft.com/library/mt612857.aspx) fornece aos cientistas de dados uma combinação do código R, dos dados do SQL Server e das funções personalizadas do SQL para compilar e implantar um modelo de R para o SQL Server.

- O passo a passo [Utilizar o T-SQL com os Serviços de R do SQL Server](https://msdn.microsoft.com/library/mt683480.aspx) proporciona aos programadores de SQL uma experiência de criar uma solução de análise avançada com o Transact-SQL usando os Serviços de R do SQL Server para operacionalizar uma solução de R.

- O passo a passo [Utilizar o T-SQL com Serviços Python do SQL Server](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) fornece aos programadores do SQL a experiência de criação de uma solução de Machine Learning no SQL Server. Ele demonstra como incorporar o Python em um aplicativo adicionando um código do Python aos procedimentos armazenados.

## <a name="whats-next"></a>O que vem a seguir?
Para obter uma visão geral dos tópicos que orientam as tarefas que compõem o processo de ciência de dados no Azure, veja [Processo de Ciência de Dados](http://aka.ms/datascienceprocess). 


