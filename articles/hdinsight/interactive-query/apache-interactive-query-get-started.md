---
title: Use a consulta interativa com o Azure HDInsight
description: Saiba como usar a Consulta Interativa (Hive LLAP) com o HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: c7cee3dfd3b091d75f4dadcaa62513fddf0c0e68
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692141"
---
# <a name="use-interactive-query-with-hdinsight"></a>Usar a Consulta Interativa com o HDInsight
Consulta Interativa (também chamado Apache Hive LLAP ou [Processamento Analítico de Baixa Latência](https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um [tipo de cluster do Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). A Consulta Interativa dá suporte ao cache na memória, o que torna as consultas do Apache Hive mais rápidas e muito mais interativas.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Um cluster de Consulta Interativa é diferente de um cluster Apache Hadoop. Ele contém apenas o serviço do Hive. 

> [!NOTE]  
> É possível acessar o serviço do Hive no cluster da Consulta Interativa somente por meio da exibição do Hive do Apache Ambari, do Beeline e do driver Microsoft Hive Open Database Connectivity (Hive ODBC). Não é possível acessá-lo por meio do console do Hive, do Templeton, da CLI Clássica do Azure nem do Azure PowerShell. 

## <a name="create-an-interactive-query-cluster"></a>Criar um cluster de Consulta Interativa
Para obter mais informações sobre a criação de um cluster HDInsight, veja [Criar clusters Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Escolha o tipo de cluster Consulta Interativa.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Executar consultas de Apache Hive da Consulta Interativa
Para executar consultas de Hive, você tem as seguintes opções:

* Usar o Microsoft Power BI

    Consulte [Visualizar dados de Hive de consulta interativa Apache Hive com o Power BI no Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) Consulte [Visualizar Big Data com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Usar Apache Zeppelin

    Ver [usar o Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).

* Usar o Virtual Studio

    Consulte [Conectar-se ao Azure HDInsight e executar consultas Apache Hive usando Ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Usar o Visual Studio Code

    Veja [Usar o Visual Studio Code para o Apache Hive, o LLAP ou o pySpark](../hdinsight-for-vscode.md).
* Execute o Apache Hive usando a exibição do Hive do Apache Ambari.
  
    Use o [Apache Ambari Hive View com o Apache Hadoop no HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Execute o Apache Hive usando o Beeline.
  
    Consulte [Usar Apache Hive com Apache Hadoop no HDInsight com Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    É possível usar o Beeline no nó de cabeçalho ou em um nó de borda vazio. Recomendamos usar o Beeline em um nó de borda vazio. Para obter informações sobre como criar um cluster HDInsight usando um nó de borda vazio, consulte [Use empty edge nodes in HDInsight](../hdinsight-apps-use-edge-node.md) (Usar nós de borda vazios no HDInsight).
* Execute o Apache Hive usando o Hive ODBC.
  
    Veja [Conectar o Excel ao Apache Hadoop com o driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Para localizar a cadeia de conexão do JDBC (Java Database Connectivity):

1. Entre no Apache Ambari usando a seguinte URL: https://\<nome do cluster\>.AzureHDInsight.net.
2. No menu à esquerda, selecione **Hive**.
3. Para copiar a URL, selecione o ícone da área de transferência:
   
   ![JDBC LLAP da Consulta Interativa no Hadoop HDInsight](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar clusters da Consulta Interativa no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Saiba como [visualizar Big Data com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Saiba como [usar o Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).
* Saiba como [executar consultas Apache Hive usando as Ferramentas do Data Lake para o Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).
* Aprenda como [usar as Ferramentas do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* Saiba como [usar o Apache Ambari Hive View com o Apache Hadoop no HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Saiba como [usar o Beeline para enviar consultas de Apache Hive no HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Saiba como [conectar o Excel ao Apache Hadoop com o driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

