---
title: "Monitorar HBase com o Conjunto de Gerenciamento de Operações (OMS) - Microsoft Azure HDInsight | Microsoft Docs"
description: Use o OMS com o Azure Log Analytics para monitorar clusters do HBase no HDInsight.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: f78d570cfa8b040cd7673a5e14e6a992511f60bb
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="monitor-hbase-with-operations-management-suite-oms"></a>Monitorar o HBase com o OMS (Operations Management Suite)

O Monitoramento do HBase no HDInsight usa o Azure Log Analytics para coletar métricas de desempenho do HBase no HDInsight de seus nós de cluster do HDInsight. O Monitor fornece visualizações específicas de HBase, além de painéis de controle, ferramentas para pesquisar as métricas e a capacidade de criar alertas e regras de monitoramento personalizadas. Você pode monitorar as métricas para vários clusters HBase no HDInsight entre várias assinaturas do Azure.

O Log Analytics é um serviço no [OMS (Operations Management Suite)](../../operations-management-suite/operations-management-suite-overview.md) que monitora seus ambientes na nuvem e locais a fim de manter a disponibilidade e o desempenho. O Log Analytics coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento para fornecer análise de várias fontes.

[As soluções de gerenciamento do Log Analytics](../../log-analytics/log-analytics-add-solutions.md) adicionam funcionalidade ao OMS, fornecendo dados adicionais e ferramentas de análise. As soluções de gerenciamento do Log Analytics são uma coleção de regras de lógica, visualização e aquisição de dados que fornecem métricas para uma área específica. Uma solução também pode definir novos tipos de registro a serem coletadas, e esses registros podem ser analisados com pesquisas de logs ou com novos recursos de interface do usuário.

O [OMS Insight and Analytics](https://azure.microsoft.com/pricing/details/insight-analytics/) é compilado na plataforma do Log Analytics. Você pode optar por usar as funcionalidades do Log Analytics e pagar por GB introduzido no serviço ou transferir o espaço de trabalho para a camada do OMS Insight and Analytics e pagar por nó gerenciado pelo serviço. O OMS Insight and Analytics oferece um superconjunto das funcionalidades oferecidas pelo Log Analytics. A solução de monitoramento do HBase está disponível com o Log Analytics ou o OMS Insight and Analytics.

Quando você provisiona uma solução de Monitoramento do HBase no HDInsight, você cria um espaço de trabalho do OMS. Cada espaço de trabalho como um ambiente exclusivo do Log Analytics com seu próprio repositório de dados, fontes de dados e soluções. É possível criar vários espaços de trabalho na sua assinatura para dar suporte a vários ambientes como produção e teste.

## <a name="provision-hdinsight-hbase-monitoring"></a>Provisionar Monitoramento do HBase no HDInsight

1. Entre no [Portal do Azure](https://portal.azure.com) usando sua assinatura do Azure.
2. No painel **Novo** em **Marketplace**, selecione **Monitoramento + gerenciamento**.
3. No painel **Monitoramento + gerenciamento**, selecione **Ver todos**.

    ![Monitoramento + painel de gerenciamento](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. Na lista, procure a banda **Soluções de gerenciamento**. À direita de **Soluções de gerenciamento**, selecione **Mais**.

    ![Monitoramento + painel de gerenciamento](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. No painel **Soluções de gerenciamento**, selecione a solução de gerenciamento de Monitoramento do HBase no HDInsight para adicionar a um espaço de trabalho.

    ![Painel Soluções de gerenciamento](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. No painel de solução de gerenciamento, revise as informações sobre a solução de gerenciamento e selecione **Criar**. 
7. No painel *nome da solução de gerenciamento*, selecione um espaço de trabalho existente para associar com a solução de gerenciamento ou criar um novo espaço de trabalho do OMS e, em seguida, selecione-o.
8. Altere as configurações do espaço de trabalho em relação à assinatura do Azure, ao grupo de recursos e ao local, conforme apropriado. 
    ![espaço de trabalho da solução](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. Selecione **Criar**.  
10. Para usar essa nova solução de gerenciamento em seu espaço de trabalho, navegue até **Log Analytics** > ***nome do espaço de trabalho*** > **Soluções**. Uma entrada para a sua solução de gerenciamento é exibida na lista. Selecione a entrada para navegar até a solução.

    ![Soluções do Log Analytics](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. O painel para sua solução de Monitoramento do HBase no HDInsight aparece.

    ![Painel de Soluções do HBase no HDInsight](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. Os blocos de resumo não mostram todos os dados porque você ainda não configurou o cluster HBase no HDInsight para enviar dados para o Log Analytics.

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>Conectar o cluster do HBase no HDInsight no Log Analytics

Para usar as ferramentas fornecidas pelo Monitoramento do HBase no HDInsight, você precisa configurar o cluster para que ele transmita as métricas do seu servidor da região, nós de cabeçalho e nós de ZooKeeper para o Log Analytics. Essa configuração é feita ao executar uma ação de Script em seu cluster do HBase no HDInsight.

### <a name="get-oms-workspace-id-and-workspace-key"></a>Obter a ID de espaço de trabalho do OMS e a chave do espaço de trabalho

Você precisa da sua ID do espaço de trabalho do OMS e chave do espaço de trabalho para habilitar os nós no seu cluster para autenticar com o Log Analytics. Para obter estes valores:

1. No painel de Monitoramento do HBase no portal do Azure, selecione Visão geral.

    ![Painel de solução de Monitoramento do HBase](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. Selecione o Portal OMS para iniciar o Portal OMS em uma nova janela ou guia do navegador.

    ![Selecione Portal OMS](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. Na página inicial do Portal OMS, selecione Configurações.

    ![Portal do OMS](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. Selecione Fonte Conectada, Servidores do Linux.

    ![Selecione Fonte Conectada e, em seguida, Servidores do Linux](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. Observe os valores de ID do espaço de trabalho e chave primária exibidos, já que esses são os valores necessários para a ação de Script.

### <a name="run-the-script-action"></a>Executar a ação de script

Para habilitar a coleta de dados do seu cluster do HBase no HDInsight, execute uma ação de Script em todos os nós do cluster:

1. Navegue até o painel para seu cluster do HBase no HDInsight no portal do Azure.
2. Selecione **Ações de Script**.

    ![Ações de script](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. Selecione **Enviar novo**.

    ![Enviar novo](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. Na ação **Enviar script**, defina o tipo de Script como `"- Custom"`.
5. Forneça um nome para esse script.
6. Para o **URI do script Bash**, cole o URI a seguir:

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. Para os **Tipos de nós**, selecione todos os três (**Cabeçalho**, **Região**, **ZooKeeper**).
8. Na caixa de texto **Parâmetros**, insira sua ID do espaço de trabalho e a chave do espaço de trabalho, colocando cada valor entre aspas e separados por um espaço.

        "<Workspace ID>" "<Workspace Key>"

9. Selecione **Manter essa ação de script** para executar novamente a ação quando novos nós forem adicionados ao cluster.

    ![Configurações da ação de script](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. Selecione **Criar**.
11. A ação de script leva alguns minutos para ser executada. Você pode monitorar seu status no painel de ações de Script.

    ![Ação de script em execução](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. Quando a ação de Script for concluída, você verá uma marca de seleção verde ao lado do nome do script na lista.

    ![Ação de script concluída](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>Exibir a solução de Monitoramento do HBase no HDInsight

Após a ação de Script ser concluída, você deve ver os dados na solução de Monitoramento em alguns minutos.

1. No portal do Azure, navegue até o painel do sua solução do HBase no HDInsight.
2. Selecione o guia **Visão geral**.
3. Em **Resumo**, você vê um bloco que indica o número de Servidores de Região sendo monitorados.

    ![Bloco de resumo de monitoramento](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. Selecione o bloco com a contagem do servidor de região. O painel principal é exibido.
5. Esse painel fornece acesso às estatísticas sobre regiões, contagem de log write-ahead (WAL) em uso, uma coleção de pesquisas do Log Analytics (por exemplo, para Logs do servidor de região, logs Phoenix exceções) e uma coleção de gráficos conhecidos para visualizar métricas relevantes. 

    ![Painel principal](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. Selecionar qualquer uma destas opções irá fazer busca detalhada no modo de exibição de pesquisa de logs onde você pode refinar a consulta e explorar os dados mais detalhadamente.

## <a name="next-steps"></a>Próximas etapas

* [Criar alertas no Log Analytics do OMS](../../log-analytics/log-analytics-alerts-creating.md)
* [Localizar dados com as pesquisas de logs no Azure Log Analytics](../../log-analytics/log-analytics-log-searches.md).
