---
title: "Solução de Análise do Azure SQL no Log Analytics | Microsoft Docs"
description: "A solução de Análise do Azure SQL ajuda a gerenciar os bancos de dados do Azure SQL."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: banders
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: f5f9aa186480926df1110928983566e05f79efb8
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017


---


# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Monitorar o Banco de Dados do Azure SQL usando a Análise do Azure SQL (Visualização) no Log Analytics

![Símbolo da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-symbol.png)

A solução Análise de SQL do Azure do Azure Log Analytics coleta e visualiza métricas de desempenho importantes do SQL Azure. Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas. E você pode monitorar o Banco de Dados SQL e métricas de pool elástico em várias assinaturas do Azure e pools elásticos e visualizá-los. A solução também ajuda a identificar problemas em cada camada da pilha do aplicativo.  Ele usa [métricas de Diagnóstico do Azure](log-analytics-azure-storage.md) junto com modos de exibição de Log Analytics para apresentar dados sobre todos os bancos de dados do Azure SQL e pools elásticos em um único espaço de trabalho de Log Analytics.

Atualmente, essa solução de visualização dá suporte a até 5.000 Bancos de Dados do Azure SQL e 150.000 Pools Elásticos por espaço de trabalho.

A solução Análise de SQL do Azure, assim como outras disponíveis para o Log Analytics, ajuda você a monitorar e receber notificações sobre a integridade dos recursos do Azure – neste caso, o Banco de Dados SQL do Azure. O Banco de Dados SQL do Microsoft Azure é um serviço de banco de dados relacional escalonável que fornece recursos semelhantes aos recursos familiares do SQL Server para aplicativos em execução na nuvem do Azure. O Log Analytics ajuda a coletar, correlacionar e visualizar dados estruturados e não estruturados.

## <a name="connected-sources"></a>Fontes conectadas

A solução Análise de SQL do Azure não usa agentes para se conectar ao serviço Log Analytics.

A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| Fonte Conectada | Suporte | Descrição |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Não | Agentes diretos do Windows não são usados pela solução. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | Agentes do Linux diretos não são usados pela solução. |
| [Grupo de gerenciamento do SCOM](log-analytics-om-agents.md) | Não | Uma conexão direta do agente do SCOM ao Log Analytics não é usada pela solução. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | O Log Analytics não lê os dados pré-existentes de uma conta de armazenamento. |
| [Diagnóstico do Azure](log-analytics-azure-storage.md) | Sim | Os dados de métricas do Azure são enviados para o Log Analytics diretamente pelo Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se não tiver uma, você poderá criá-la [grátis](https://azure.microsoft.com/free/).
- Um espaço de trabalho do Log Analytics. Você pode usar um existente ou pode [criar um novo](log-analytics-get-started.md) para começar a usar essa solução.
- Habilite o Diagnóstico do Azure para os bancos de dados do Azure SQL e pools elásticos e [configure-os para enviar os dados para o Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

## <a name="configuration"></a>Configuração

Realize as etapas a seguir para adicionar a solução Análise de SQL do Azure ao seu espaço de trabalho.

1. Adicione a solução de análise do SQL do Azure do [marketplace do Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).
2. No portal do Azure, clique em **Novo** (o símbolo +) e, na lista de recursos, selecione **Monitoramento + Gerenciamento**.  
    ![Monitoramento + Gerenciamento](./media/log-analytics-azure-sql/monitoring-management.png)
3. Na lista **Monitoramento + Gerenciamento**, clique em **Ver todos**.
4. Na lista **Recomendado**, clique em **Mais** e, na nova lista, localize **Análise do Azure SQL (Visualização)** e selecione-a.  
    ![Solução de Análise do Azure SQL](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. Na folha **Análise do Azure SQL (Visualização)**, clique em **Criar**.  
    ![Criar](./media/log-analytics-azure-sql/portal-create.png)
6. Na folha **Criar nova solução**, selecione o espaço de trabalho ao qual você deseja adicionar a solução e clique em **Criar**.  
    ![adicionar ao espaço de trabalho](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Para configurar várias assinaturas do Azure

Para dar suporte a várias assinaturas, use o script do PowerShell de [Habilitar log de métricas de recursos do Azure usando o PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Forneça a ID de recurso do espaço de trabalho como um parâmetro ao executar o script para enviar dados de diagnóstico de recursos em uma assinatura do Azure a um espaço de trabalho em outra assinatura do Azure.

**Exemplo**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Usando a solução

Quando você adiciona a solução ao espaço de trabalho, o bloco de Análise do Azure SQL é adicionado ao espaço de trabalho e aparece na Visão geral. O bloco mostra o número de bancos de dados do Azure SQL e pools elásticos do Azure SQL aos quais a solução está conectada.

![Bloco de Análise do SQL Azure](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Exibindo dados da Análise de SQL do Azure

Clique no bloco **Análise de SQL do Azure** para abrir o painel da Análise de SQL do Azure. O painel inclui as folhas definidas abaixo. Cada folha lista até 15 recursos (assinatura, servidor, pool elástico e banco de dados). Clique em um dos recursos para abrir o painel do recurso específico. Pool elástico ou Banco de dados contém os gráficos com as métricas de um recurso selecionado. Clique em um gráfico para abrir a caixa de diálogo Pesquisa de Logs.

| Folha | Descrição |
|---|---|
| Assinaturas | Lista de assinaturas com o número de servidores, pools e bancos de dados conectados. |
| Servidores | Lista de servidores com o número de pools e bancos de dados conectados. |
| Pools elásticos | Lista de pools elásticos conectados com GBs e eDTUs máximos no período observado. |
|Bancos de dados | Lista de bancos de dados conectados com GBs e DTUs máximos no período observado.|


### <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

A solução inclui consultas úteis para ajudar a analisar os dados. Se você rolar para a direita, o painel listará várias consultas comuns nas quais você poderá clicar para realizar uma [pesquisa de log](log-analytics-log-searches.md) e obter dados do Azure SQL.

![consultas](./media/log-analytics-azure-sql/azure-sql-queries.png)

A solução inclui algumas *consultas baseadas em alerta*, conforme mostrado acima, que você pode usar para alertar sobre limites específicos de bancos de dados do Azure SQL e pools elásticos.

#### <a name="to-configure-an-alert-for-your-workspace"></a>Para configurar um alerta para seu espaço de trabalho

1. Acesse o [portal do OMS](http://mms.microsoft.com/) e entre.
2. Abra o espaço de trabalho que você configurou para a solução.
3. Na página Visão geral, clique no bloco **Análise do Azure SQL (Visualização)**.
4. Role para a direita e clique em uma consulta para começar a criar um alerta.  
![consulta de alerta](./media/log-analytics-azure-sql/alert-query.png)
5. Na Pesquisa de Log, clique em **Alerta**.  
![criar alerta na pesquisa](./media/log-analytics-azure-sql/create-alert01.png)
6. Na página **Adicionar Regra de Alerta**, defina as propriedades adequadas e os limites específicos que você deseja e clique em **Salvar**.  
![adicionar regra de alerta](./media/log-analytics-azure-sql/create-alert02.png)

### <a name="act-on-azure-sql-analytics-data"></a>Tomar decisões com base em dados da Análise de SQL do Azure

Por exemplo, uma das consultas mais úteis que você pode executar é comparar a utilização de DTU para todos os Pools Elásticos do Azure SQL em todas as suas assinaturas do Azure. A DTU (Unidade de Taxa de Transferência de Banco de Dados) fornece uma maneira de descrever a capacidade relativa de um nível de desempenho de pools e bancos de dados Basic, Standard e Premium. DTUs são baseadas em uma medida combinada de CPU, memória, leituras e gravações. À medida que as DTUs aumentam, aumenta a capacidade oferecida pelo nível de desempenho. Por exemplo, um nível de desempenho com cinco DTUs tem cinco vezes mais energia do que um nível de desempenho com uma DTU. Uma cota de DTU máxima aplica-se a cada servidor e pool elástico.

Executando a consulta de Pesquisa de Log a seguir, você pode saber facilmente se está subutilizando ou sobreutilizando os pools elásticos do SQL Azure.

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

No exemplo a seguir, você pode ver que um pool elástico tem alto uso de quase 100% de DTU, enquanto outros têm pouca utilização. Você pode investigar mais para solucionar possíveis alterações recentes no ambiente usando os logs de atividade do Azure.

![Resultados de pesquisa de log - alta utilização](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>Consulte também

- Usar [Pesquisas de Log](log-analytics-log-searches.md) no Log Analytics para exibir dados detalhados do Azure SQL.
- [Criar seus próprios painéis](log-analytics-dashboards.md) mostrando os dados do Azure SQL.
- [Criar alertas](log-analytics-alerts.md) quando ocorrerem eventos específicos do Azure SQL.

