---
title: Veja Visualizar Big Data com o Power BI no Azure HDInsight
description: Saiba como usar o Microsoft Power BI para visualizar dados de Hive processados pelo Azure HDInsight.
keywords: hdinsight, hadoop, hive, consulta interativa, hive interativo, LLAP, odbc
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: c24818f6b746754111540bae5fbf7f21d22c3a61
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041897"
---
# <a name="visualize-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualizar dados de Hive com o Microsoft Power BI usando ODBC no Azure HDInsight

Saiba como conectar o Microsoft Power BI ao Azure HDInsight usando ODBC e visualizar os dados do Hive. 

>[!IMPORTANT]
> Você pode aproveitar o driver ODBC Hive a ser importado por meio do conector ODBC genérico no Power BI Desktop. No entanto, não é recomendável para cargas de trabalho de BI considerando a natureza não interativo do mecanismo de consulta do Hive. [Conector de consulta interativa HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) e [conector do HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) são as melhores opções para o desempenho.

Neste tutorial, você carrega os dados de uma tabela de Hive hivesampletable no Power BI. A tabela de Hive contém alguns dados de uso de telefone celular. Em seguida, você cria gráficos com os dados de uso em um mapa mundial:

![Relatório de mapa de Power BI do HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

As informações também se aplicam ao novo tipo de cluster [Consulta Interativa](../interactive-query/apache-interactive-query-get-started.md). Para saber como se conectar à consulta interativa do HDInsight usando a consulta direta, veja [Visualizar dados de Hive de consulta interativa com o Microsoft Power BI usando consulta direta no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).



## <a name="prerequisites"></a>Pré-requisitos
Antes de prosseguir com este artigo, você deve ter os seguintes itens:

* **Cluster HDInsight**. O cluster pode ser um cluster HDInsight com Hive ou um cluster de Consulta Interativa recém-lançado. Para a criação de clusters, consulte [Criar cluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Você pode baixar uma cópia do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Criar uma fonte de dados ODBC do Hive

Consulte [Criar uma fonte de dados ODBC do Hive](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

A tabela de Hive hivesampletable acompanha todos os clusters HDInsight.

1. Entre no Power BI Desktop.
2. Clique no ícone **Início**, clique em **Obter Dados** na faixa de opções **Dados externos** e, em seguida, selecione **Mais**.

    ![Abrir dados do Power BI do HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. Do painel **Obter Dados**, clique em **Outros** na esquerda, clique em **ODBC** na direita e, em seguida, clique em **Conectar** na parte inferior.
4. Do painel **ODBC**, selecione o nome da fonte de dados que você criou na última seção e clique em **OK**.
5. Do painel **Navegador**, expanda **ODBC->HIVE->padrão**, selecione **hivesampletable** e, em seguida, clique em **Carregar**.

## <a name="visualize-data"></a>Visualizar dados

Continue do último procedimento.

1. No painel Visualizações, selecione **Mapa**.  Ele é um ícone de globo.

    ![O Power BI do HDInsight personaliza o relatório](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. No painel Campos, selecione **país** e **devicemake**. Você pode ver os dados no gráfico no mapa.
3. Expanda o mapa.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como visualizar os dados de HDInsight usando o Power BI.  Para saber mais, consulte os seguintes artigos:

* [Usar o Zeppelin para executar consultas do Hive no Azure HDInsight ](./../hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conectar o Excel ao Hadoop usando Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conectar-se ao Azure HDInsight e executar consultas Hive usando Ferramentas do Data Lake para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregue os Dados no HDInsight](./../hdinsight-upload-data.md).
