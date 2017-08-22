---
title: "Solução de problemas do Spark – Azure HDInsight | Microsoft Docs"
description: Use as perguntas frequentes sobre o Spark para obter respostas a perguntas comuns sobre Storm na plataforma do Azure HDInsight.
keywords: "Azure HDInsight, Spark, perguntas frequentes, guia de solução de problemas, problemas comuns, configuração de aplicativo, Ambari"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 7655acd689c3f5b869eb0ddb5f186cff032ca8b6
ms.contentlocale: pt-br
ms.lasthandoff: 07/11/2017


---

# <a name="spark-troubleshooting"></a>Solução de problemas do Spark

Este artigo descreve os principais problemas e suas resoluções para trabalhar com conteúdo do Spark no Apache Ambari.

## <a name="how-do-i-configure-a-spark-application-through-ambari-on-clusters"></a>Como fazer para configurar um aplicativo Spark por meio de Ambari nos clusters

### <a name="issue"></a>Problema:

É necessário configurar no Ambari a quantidade de memória e o número de núcleos que um aplicativo Spark pode usar.  

### <a name="resolution-steps"></a>Etapas de Resolução: 

Esse procedimento configura valores definidos anteriormente em clusters do HDInsight Spark. Consulte [Por que meu aplicativo Spark falhou com OutOfMemoryError?](#why-did-my-spark-application-fail-with-an-outofmemoryerror) para determinar quais configurações do Spark precisam ser definidas e para quais valores.

1. Escolha **Spark2** na lista de clusters.

    ![Selecione o cluster na lista](media/hdinsight-troubleshoot-spark/update-config-1.png)

1. Selecione a guia **Configurações** .

    ![Escolha a guia Configurações](media/hdinsight-troubleshoot-spark/update-config-2.png)

1. Na lista de configurações, escolha **Custom-spark2-defaults**.

    ![Escolha custom-spark-defaults](media/hdinsight-troubleshoot-spark/update-config-3.png)

1. Procure a configuração do valor que você precisa ajustar, como **spark.executor.memory**. Nesse caso, o valor de 4608m é alto demais.

    ![Selecione o campo spark.executor.memory](media/hdinsight-troubleshoot-spark/update-config-4.png)

1. Defina o valor como recomendado. Nesse caso, 2048 é recomendado para essa configuração.

    ![Altere o valor para 2048m](media/hdinsight-troubleshoot-spark/update-config-5.png)

1. Salve o valor da configuração e, em seguida, salve a configuração. 

    Clique em **Salvar** na barra de ferramentas.

    ![Salvar a configuração e a configuração](media/hdinsight-troubleshoot-spark/update-config-6a.png)

    Você será notificado se todas as configurações precisarem de atenção. Tome nota delas e clique em **Continuar Mesmo Assim**. 

    ![Clique em Continuar Mesmo Assim](media/hdinsight-troubleshoot-spark/update-config-6b.png)

    Escreva uma observação sobre as alterações de configuração e clique em **Salvar**.

    ![Digite uma observação sobre as alterações feitas](media/hdinsight-troubleshoot-spark/update-config-6c.png)

1. Sempre que uma configuração é salva, você é solicitado a reiniciar o serviço. Clique em **Reiniciar**.

    ![Clique em reiniciar](media/hdinsight-troubleshoot-spark/update-config-7a.png)

    Confirme a reinicialização.

    ![Clique em Confirmar Reiniciar Tudo](media/hdinsight-troubleshoot-spark/update-config-7b.png)

    Você pode examinar os processos em execução.

    ![Examinar processos em execução](media/hdinsight-troubleshoot-spark/update-config-7c.png)

1. Você também pode adicionar configurações. Na lista de configurações, escolha **Custom-spark2-defaults** como fez na Etapa 3 e, em seguida, escolha **Adicionar Propriedade**.

    ![Clique em adicionar propriedade](media/hdinsight-troubleshoot-spark/update-config-8.png)

1. Defina uma nova propriedade. Você pode definir uma propriedade única, com uma caixa de diálogo para configurações específicas, como tipo de dados ou pode definir várias propriedades com uma configuração por linha. 

    Neste exemplo, a propriedade **spark.driver.memory** é definida com um valor de 4g.

    ![Definir nova propriedade](media/hdinsight-troubleshoot-spark/update-config-9.png)

1. Salve a configuração e reinicie o serviço conforme descrito nas etapas 6 e 7.

Essas alterações são para todo o cluster, mas podem ser substituídas no momento do envio do trabalho do Spark real.

### <a name="further-reading"></a>Leitura Adicional:

[Envio de trabalho do Spark em clusters do HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters"></a>Como fazer para configurar um aplicativo Spark por meio de um bloco de anotações do Jupyter nos clusters

### <a name="issue"></a>Problema:

É necessário configurar a quantidade de memória e o número de núcleos que um aplicativo Spark pode usar ao utilizar um bloco de anotações do Jupyter em clusters do HDInsight. 

1. Consulte o tópico [Por que meu aplicativo Spark falhou com OutOfMemoryError?](#spark-application-failure-outofmemory) para determinar quais configurações do Spark precisam ser definidas e para quais valores.
1.  Especifique as configurações do Spark em um formato JSON válido na primeira célula do bloco de anotações do Jupyter após a diretiva %%configure (altere os valores reais conforme aplicável): 

>![Adicionar uma configuração](media/hdinsight-troubleshoot-spark/add-configuration-cell.png)

### <a name="further-reading"></a>Leitura Adicional:

[Envio de trabalho do Spark em clusters do HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-livy-on-clusters"></a>Como fazer para configurar um aplicativo Spark por meio de LIVY nos clusters

### <a name="issue"></a>Problema:

É necessário configurar no momento do envio por meio do LIVY a quantidade de memória e o número de núcleos que um aplicativo Spark pode usar em clusters do HDInsight. 

1. Consulte o tópico [Por que meu aplicativo Spark falhou com OutOfMemoryError?](#spark-application-failure-outofmemory) para determinar quais configurações do Spark precisam ser definidas e para quais valores.
1. Envie o aplicativo Spark para o LIVY usando um cliente REST como CURL com um comando semelhante ao seguinte (altere os valores reais conforme aplicável):

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

### <a name="further-reading"></a>Leitura Adicional:

[Envio de trabalho do Spark em clusters do HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-spark-submit-on-clusters"></a>Como fazer para configurar um aplicativo Spark por meio de spark-submit clusters

### <a name="issue"></a>Problema: 

É necessário configurar no momento do envio por meio do spark-submit a quantidade de memória e o número de núcleos que um aplicativo Spark pode usar em clusters do HDInsight.

1. Consulte o tópico [Por que meu aplicativo Spark falhou com OutOfMemoryError?](#spark-application-failure-outofmemory) para determinar quais configurações do Spark precisam ser definidas e para quais valores.
1. Inicialize o spark-shell com um comando semelhante ao seguinte (altere o valor real das configurações conforme aplicável): 

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="further-reading"></a>Leitura Adicional:

[Envio de trabalho do Spark em clusters do HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)



## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>O que causa uma exceção OutOfMemoryError de um aplicativo Spark

### <a name="error"></a>Erro:

O Aplicativo Spark falhou com uma exceção OutOfMemoryError.

### <a name="detailed-description"></a>Descrição detalhada:

O aplicativo Spark falhou com os seguintes tipos de exceções não capturadas.  
```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Causa provável:

A causa mais provável dessa exceção é a memória heap insuficiente alocada para a Máquina Virtual Java (JVM) inicializada como os executores ou driver como parte do aplicativo Spark. 

### <a name="resolution-steps"></a>Etapas de Resolução:

1. Determine o tamanho máximo dos dados com os quais o aplicativo Spark lidará. Uma previsão pode ser feita com base no tamanho máximo dos dados de entrada, nos dados intermediários produzidos transformando os dados de entrada e os dados de saída produzidos, transformando ainda mais os dados intermediários. Poderá ser um processo iterativo também se a previsão inicial formal não for possível. 
1. Verifique se o cluster HDInsight a ser usado tem recursos suficientes em termos de memória e também núcleos para aceitar o aplicativo Spark. Isso pode ser determinado consultando a seção Métrica do Cluster da IU YARN do cluster para obter os valores da Memória Usada versus Total de Memória e VCores Usados versus VCores Totais.

1. Defina as seguintes configurações do Spark para os valores apropriados que não excedem 90% da memória disponível e núcleos conforme o exibido pelo YARN, ainda bem dentro dos requisitos de memória do aplicativo Spark: 

```apache
spark.executor.instances (Example: 8 for 8 executor count) 
spark.executor.memory (Example: 4g for 4 GB) 
spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
spark.executor.cores (Example: 2 for 2 cores per executor) 
spark.driver.memory (Example: 8g for 8GB) 
spark.driver.cores (Example: 4 for 4 cores)   
spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
```

Memória total usada por todos os executores = 
```apache
spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
```
Memória total usada pelo driver = 
```apache
spark.driver.memory + spark.yarn.driver.memoryOverhead
```

### <a name="further-reading"></a>Leitura Adicional:

- [Visão geral do gerenciamento de memória do Spark](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Depuração de aplicativo Spark em clusters do HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)










