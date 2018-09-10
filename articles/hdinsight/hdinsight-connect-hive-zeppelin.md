---
title: Usar Zeppelin para executar consultas do Hive no Azure HDInsight
description: Saiba como usar o Zeppelin para executar consultas do Hive.
keywords: hdinsight, hadoop, hive, consulta interativa, LLAP
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: 3064c9cd141458307891f666bd5af9aa738cc021
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43093161"
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Usar Zeppelin para executar consultas do Hive no Azure HDInsight 

Clusters de Consulta Interativa do HDInsight incluem blocos de anotações do Zeppelin que você pode usar para executar consultas interativas do Hive. Neste artigo, você aprenderá a usar Zeppelin para executar consultas interativas do Hive no Azure HDInsight. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de prosseguir com este artigo, você deve ter os seguintes itens:

* **Cluster de Consulta Interativa do HDInsight**. Consulte [Criar cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) para criar um cluster HDInsight.  Verifique se você escolheu o tipo de Consulta Interativa. 

## <a name="create-a-zeppelin-note"></a>Criar uma Anotação do Zeppelin

1. Navegue até a URL a seguir:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Substitua **CLUSTERNAME** pelo nome do cluster.

2. Insira seu nome de usuário e senha do Hadoop. Na página do Zeppelin, você pode criar uma nova anotação ou abrir anotações existentes. O HiveSample contém algumas amostras de consultas do Hive.  

    ![HDInsight Consulta Interativa Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Clique em **Criar nova Anotação**.
4. Digite ou selecione os valores a seguir:

    - Nome da anotação: digite um nome para a anotação.
    - Interpretador padrão: selecione **JDBC**.

5. Clique em **Criar Anotação**.
6. Execute a consulta do Hive a seguir:

        %jdbc(hive)
        show tables

    ![HDInsight Consulta Interativa Zeppelin executa a consulta](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    A instrução **%jdbc(hive)** na primeira linha informa o bloco de anotações para usar o interpretador JDBC do Hive.

    A consulta deverá retornar uma tabela de Hive denominada *hivesampletable*.

    A seguir estão mais duas consultas de Hive que podem ser executadas na hivesampletable. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Em comparação com o Hive tradicional, os resultados da consulta retornam muito mais rapidamente.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como visualizar os dados de HDInsight usando o Power BI.  Para saber mais, consulte os seguintes artigos:

* [Visualizar dados de Hive com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados da consulta interativa do Hive com o Power BI no Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conectar o Excel ao Hadoop usando Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Conectar-se ao Azure HDInsight e executar consultas Hive usando Ferramentas do Data Lake para Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md).
* [Carregue os Dados no HDInsight](./hdinsight-upload-data.md).
