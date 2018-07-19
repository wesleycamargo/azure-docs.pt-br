---
title: Solução de Análise do Azure SQL no Log Analytics | Microsoft Docs
description: Solução de Análise do Azure SQL ajuda a gerenciar os bancos de dados do Azure SQL
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: f57a47677f752a644975a25fa746d78bced5d766
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133189"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Monitore o Banco de Dados SQL do Azure usando a Análise de SQL do Azure (Versão prévia)

![Símbolo da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Análise do SQL Azure é uma solução de monitoramento para monitorar o desempenho de bancos de dados SQL do Azure em grande escala em vários pools Elásticos e assinaturas de nuvem. Ele coleta e visualiza métricas de desempenho importantes do banco de dados SQL com inteligência interna para solução de problemas na parte superior. 

Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas. A solução ajuda a identificar problemas em cada camada da pilha do aplicativo. Ele usa métricas de Diagnóstico do Azure com modos de exibição de Log Analytics para apresentar dados sobre todos os bancos de dados do Azure SQL e pools elásticos em um único espaço de trabalho de Log Analytics. O Log Analytics ajuda a coletar, correlacionar e visualizar dados estruturados e não estruturados.

Atualmente, essa solução de visualização dá suporte a até 5.000 Bancos de Dados do Azure SQL e 150.000 Pools Elásticos por espaço de trabalho.

Para uma visão geral prática sobre o uso da solução da Análise de SQL do Azure e para cenários de uso típicos, assista o vídeo inserido:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Fontes conectadas

Análise do SQL Azure é uma nuvem monitoramento solução suporte de streaming de telemetria de diagnóstico para pools Elásticos e bancos de dados SQL do Azure. Como ele não usa agentes para se conectar ao serviço Log Analytics, a solução não dá suporte a conectividade com o Windows, Linux ou recursos do SCOM, consulte a tabela de compatibilidade abaixo.

| Fonte Conectada | Suporte | DESCRIÇÃO |
| --- | --- | --- |
| **[Diagnóstico do Azure](log-analytics-azure-storage.md)** | **Sim** | Os dados de métrica e de log do Azure são enviados ao Log Analytics diretamente pelo Azure. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não  | O Log Analytics não lê os dados pré-existentes de uma conta de armazenamento. |
| [Agentes do Windows](log-analytics-windows-agent.md) | Não  | Agentes diretos do Windows não são usados pela solução. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não  | Agentes do Linux diretos não são usados pela solução. |
| [Grupo de gerenciamento do SCOM](log-analytics-om-agents.md) | Não  | Uma conexão direta do agente do SCOM ao Log Analytics não é usada pela solução. |

## <a name="configuration"></a>Configuração

Realize as etapas a seguir para adicionar a solução Análise de SQL do Azure ao seu espaço de trabalho.

1. Adicione a solução Análise de SQL do Azure ao seu espaço de trabalho do [espaço de trabalho do Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. No portal do Azure, clique em **+ Criar um recurso**, em seguida, procure **análise de SQL do Azure**.  
    ![Monitoramento + Gerenciamento](./media/log-analytics-azure-sql/monitoring-management.png)
3. Selecione **análise de SQL do Azure (Versão prévia)** da lista
4. Na área **Análise de SQL do Azure (versão prévia)**, clique em **Criar**.  
    ![Criar](./media/log-analytics-azure-sql/portal-create.png)
5. Na área **Criar nova solução**, crie uma nova, ou selecione o espaço de trabalho ao qual deseja adicionar a solução e clique em **Criar**.  
    ![adicionar ao espaço de trabalho](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Configurar Pools Elásticos e bancos de dados SQL do Azure para telemetria de diagnóstico de fluxo

Depois que você criou uma solução de análise de SQL do Azure em seu espaço de trabalho, para monitorar o desempenho de bancos de dados SQL do Azure e/ou Pools Elásticos, você precisará **configurar cada** banco de dados SQL e recursos do pool Elástico que desejar monitorar para transmitir sua telemetria de diagnóstico à solução.

- Habilite o Diagnóstico do Azure para os bancos de dados do Azure SQL e pools elásticos e [configure-os para enviar os dados para o Log Analytics](../sql-database/sql-database-metrics-diag-logging.md).

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

Clique no bloco **Análise de SQL do Azure** para abrir o painel da Análise de SQL do Azure. O painel inclui a visão geral de todos os bancos de dados monitorados por meio de diferentes perspectivas. Para que as diferentes perspectivas funcionem, é necessário permitir que métricas ou logs adequados em seus recursos SQL sejam transmitidos para o espaço de trabalho do Azure Log Analytics.

![Visão geral da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Selecionar qualquer bloco abre um relatório de detalhamento sobre a perspectiva específica. Quando a perspectiva for selecionada, o relatório de dril down será aberto.

![Tempos limite da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Cada perspectiva fornece resumos sobre a assinatura, o servidor, o pool elástico e o nível de banco de dados. Além disso, cada perspectiva mostrada é específica ao relatório à direita. Selecionar a assinatura, o servidor, o pool ou o banco de dados na lista dá continuidade ao drill down.

| Perspectiva | DESCRIÇÃO |
| --- | --- |
| Recurso por tipo | Perspectiva que conta todos os recursos monitorados. O drill down fornece um resumo das métricas DTU e GB. |
| Insights | Fornece o drill down hierárquico para Insights inteligentes. Saiba mais sobre insights inteligentes. |
| Errors | Fornece drill down hierárquico sobre erros do SQL que ocorreram nos bancos de dados. |
| Tempos limite | Fornece drill down hierárquico sobre tempos limite do SQL que ocorreram nos bancos de dados. |
| Bloqueios | Fornece drill down hierárquico sobre bloqueios do SQL que ocorreram nos bancos de dados. |
| Esperas do banco de dados | Fornece drill down hierárquico sobre estatísticas de espera do SQL sobre o nível do banco de dados. Inclui resumos de tempo de espera total e o tempo de espera por tipo de espera. |
| Duração da consulta | Fornece drill down hierárquico sobre as estatísticas de execução de consulta como duração da consulta, uso da CPU, uso de E/S dos dados, uso de E/S do log. |
| Esperas da consulta | Fornece drill down hierárquico sobre as estatísticas de espera da consulta por categoria de espera. |

### <a name="intelligent-insights-report"></a>Relatório de Insights inteligentes

O [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) do Banco de dados SQL do Azure permite que você saiba o que está acontecendo com o desempenho do banco de dados. Todos os insights inteligentes coletados podem ser visualizados e acessados por meio da perspectiva de Insights.

![Insights de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Relatórios do pool elástico e do banco de dados

Os pools elásticos e os bancos de dados têm seus próprios relatórios específicos que mostram todos os dados coletados para o recurso no horário especificado.

![Banco de dados de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Pool Elástico de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Por meio das perspectivas de Duração da consulta e Espera da consulta, é possível correlacionar o desempenho de qualquer consulta por meio do relatório de consulta. Este relatório compara o desempenho de consultas em bancos de dados diferentes e torna mais fácil identificar bancos de dados que executam a consulta selecionada em comparação com as lentas.

![Consultas de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

Você pode [criar alertas](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) facilmente com os dados provenientes de recursos de Banco de Dados SQL do Azure. Estas são algumas das consultas de [pesquisa de logs](log-analytics-log-searches.md) úteis que você pode usar com um alerta do log:



*DTU alta no Banco de Dados SQL do Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Alta DTU no pool elástico do Banco de Dados SQL do Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>Próximas etapas

- Usar [Pesquisas de Log](log-analytics-log-searches.md) no Log Analytics para exibir dados detalhados do Azure SQL.
- [Criar seus próprios painéis](log-analytics-dashboards.md) mostrando os dados do Azure SQL.
- [Criar alertas](log-analytics-alerts.md) quando ocorrerem eventos específicos do Azure SQL.
