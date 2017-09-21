---
title: Usar o Hive Interativo com o Azure HDInsight | Microsoft Docs
description: Saiba como usar o Hive Interativo (Hive LLAP) com o HDInsight.
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 6c4e3d0e97eb9ad4500d684c7da3b19c5669994f
ms.contentlocale: pt-br
ms.lasthandoff: 09/09/2017

---
# <a name="use-interactive-hive-with-hdinsight-preview"></a>Usar o Hive Interativo com o HDInsight (versão prévia)
O Hive Interativo (também chamado de Hive LLAP ou [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um novo [tipo de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) Azure HDInsight. O Hive Interativo dá suporte ao cache na memória, o que torna as consultas do Hive mais rápidas e muito mais interativas. 

Um cluster do Hive Interativo é diferente de um cluster Hadoop. Ele contém apenas o serviço do Hive. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie e outros serviços, em breve, serão removidos desse tipo de cluster.
> É possível acessar o serviço do Hive no cluster do Hive Interativo somente por meio da exibição do Hive do Ambari, do Beeline e do driver Microsoft Hive Open Database Connectivity (Hive ODBC). Não é possível acessá-lo por meio do console do Hive, Templeton, a CLI do Azure (ferramenta de linha de comando do Azure) ou Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>Criar um cluster do Hive Interativo
É possível usar clusters do Hive Interativo apenas em clusters baseados em Linux. Para obter informações sobre como criar um cluster HDInsight, consulte [Criar clusters Hadoop baseados em Linux em HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="execute-hive-queries-from-interactive-hive"></a>Executar consultas de Hive do Hive Interativo
Para executar consultas de Hive, você tem as seguintes opções:

* Execute o Hive usando a exibição do Hive do Ambari.
  
    Para obter informações sobre como usar a Exibição de Hive, consulte [Use the Hive View with Hadoop in HDInsight](hdinsight-hadoop-use-hive-ambari-view.md) (Usar a exibição de Hive com o Hadoop no HDInsight).
* Execute o Hive usando o Beeline.
  
    Para obter informações sobre como usar o Beeline no HDInsight, consulte [Use Hive with Hadoop in HDInsight with Beeline](hdinsight-hadoop-use-hive-beeline.md) (Usar o Hive com o Hadoop no HDInsight com Beeline).
  
    É possível usar o Beeline no nó de cabeçalho ou em um nó de borda vazio. Recomendamos usar o Beeline em um nó de borda vazio. Para obter informações sobre como criar um cluster HDInsight usando um nó de borda vazio, consulte [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Usar nós de borda vazios no HDInsight).
* Execute o Hive usando o ODBC do Hive.
  
    Para obter informações sobre como usar o ODBC do Hive, consulte [Conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).

Para localizar a cadeia de conexão do JDBC (Java Database Connectivity):

1. Entre no Ambari usando a seguinte URL: https://\<nome do cluster\>.AzureHDInsight.net.
2. No menu à esquerda, selecione **Hive**.
3. Para copiar a URL, selecione o ícone da área de transferência:
   
   ![JDBC LLAP do Hive Interativo no Hadoop HDInsight](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba como [criar clusters do Hive Interativo no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Saiba como [usar o Beeline para enviar consultas de Hive no HDInsight](hdinsight-hadoop-use-hive-beeline.md).
* Saiba como [conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).


