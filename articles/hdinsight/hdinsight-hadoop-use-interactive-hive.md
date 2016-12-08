---
title: Usar o Hive Interativo no HDInsight | Microsoft Docs
description: Saiba como usar o Hive Interativo (Hive no LLAP) no HDInsight.
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6971e123b388eab689e02a5c67809509c2ad29c6


---
# <a name="use-interactive-hive-in-hdinsight-preview"></a>Usar o Hive Interativo no HDInsight (Visualização)
O Hive Interativo (também conhecido como [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um novo [tipo de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) do HDInsight.  O Hive Interativo permite o armazenamento em cache na memória, o que torna as consultas do Hive muito mais interativas e rápidas. Esse novo recurso torna o HDInsight uma das soluções de Big Data de melhor desempenho, flexível e aberta na nuvem com caches na memória (usando Hive e Spark) e análise avançada por meio de integração profunda aos serviços de R. 

O cluster Hive Interativo é diferente do cluster Hadoop. Ele contém apenas o serviço do Hive. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie e outros serviços, em breve, serão removidos desse tipo de cluster.
> O serviço Hive no cluster do Hive Interativo só pode ser acessado por meio da exibição Hive do Ambari, do Beeline e do ODBC do Hive. Ele não pode ser acessado por meio do console do Hive, do Templeton, da CLI do Azure e do Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>Criar um cluster do Hive Interativo
O cluster do Hive Interativo é compatível apenas com clusters baseados em Linux. Para obter informações sobre a criação de clusters HDInsight, confira [Criar clusters Hadoop baseados em Linux em HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="execute-hive-from-interactive-hive"></a>Executar o Hive do Hive Interativo
Há diferentes opções para executar consultas do Hive:

* Executar o Hive usando a exibição Hive do Ambari
  
    Para saber mais sobre como usar a Exibição Hive, confira [Use the Hive View with Hadoop in HDInsight](hdinsight-hadoop-use-hive-ambari-view.md) (Usar a exibição Hive com o Hadoop no HDInsight).
* Executar o Hive usando o Beeline
  
    Para obter informações sobre como usar o Beeline no HDInsight, confira [Usar o Hive com o Hadoop no HDInsight com Beeline](hdinsight-hadoop-use-hive-beeline.md).
  
    Use o Beeline no nó de cabeçalho ou em um nó de borda vazio.  É recomendável usar o Beeline em um nó de borda vazio.  Para saber mais sobre como criar um cluster HDInsight com um nó de borda vazio, confira [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md).
* Executar o Hive usando o ODBC do Hive
  
    Para obter informações sobre como usar o ODBC do Hive, confira [Conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).

**Para encontrar a cadeia de conexão JDBC:**

1. Entre no Ambari usando a seguinte URL: https://<ClusterName>.AzureHDInsight.net.
2. Clique em **Hive** no menu à esquerda.
3. Clique no ícone realçado para copiar a URL:
   
   ![JDBC LLAP do Hive Interativo no Hadoop HDInsight](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>Consulte também
* [Criar clusters Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md): saiba como criar clusters Hive Interativo no HDInsight.
* [Usar o Hive com o Hadoop no HDInsight com Beeline](hdinsight-hadoop-use-hive-beeline.md): saiba como usar o Beeline para enviar consultas do Hive.
* [Conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md): saiba como conectar o Excel ao Hive.




<!--HONumber=Nov16_HO3-->


