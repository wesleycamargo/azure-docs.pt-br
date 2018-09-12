---
title: Solução de Análise do Azure SQL no Log Analytics | Microsoft Docs
description: Solução de Análise do Azure SQL ajuda a gerenciar os bancos de dados do Azure SQL
services: log-analytics
documentationcenter: ''
author: danimir
manager: carmonm
ms.reviewer: carlrab
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: v-daljep
ms.component: na
ms.openlocfilehash: 82845f475857f9a911febd496e86eb2a60f69c25
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782219"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Monitore o Banco de Dados SQL do Azure usando a Análise de SQL do Azure (Versão prévia)

![Símbolo da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-symbol.png)

A Análise de SQL do Azure é uma solução de monitoramento em nuvem para monitorar o desempenho de Bancos de Dados SQL do Azure, pools elásticos e Instâncias Gerenciadas em escala e em várias assinaturas. Ela coleta e visualiza importantes métricas de desempenho do Banco de Dados SQL do Azure com inteligência interna para solução de problemas de desempenho.

Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas. A solução ajuda a identificar problemas em cada camada da pilha do aplicativo. Ela usa as métricas de Diagnóstico do Azure junto com as exibições do Log Analytics para apresentar dados sobre todos os Bancos de Dados SQL do Azure, pools elásticos e bancos de dados em Instâncias Gerenciadas em um único espaço de trabalho de Log Analytics. O Log Analytics ajuda a coletar, correlacionar e visualizar dados estruturados e não estruturados.

Atualmente, essa solução em versão prévia dá suporte a até 200.000 Banco de Dados SQL do Azure e 5.000 Pools Elásticos do SQL por espaço de trabalho.

Para uma visão geral prática sobre o uso da solução da Análise de SQL do Azure e para cenários de uso típicos, assista o vídeo inserido:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Fontes conectadas

A Análise de SQL do Azure é uma solução de monitoramento somente em nuvem que dá suporte ao streaming de telemetria de diagnóstico para Bancos de Dados SQL do Azure, pools elásticos e Instâncias Gerenciadas. Como não usa agentes para conectar o serviço do Log Analytics, a solução não dá suporte a monitoramento de VMs ou SQL Servers locais, consulte a tabela de compatibilidade abaixo.

| Fonte Conectada | Com suporte | DESCRIÇÃO |
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

Os Pools Elásticos e os Bancos de Dados têm seus próprios relatórios específicos que mostram todos os dados coletados para o recurso no horário especificado.

![Banco de dados de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Pool Elástico de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Por meio das perspectivas de Duração da consulta e Espera da consulta, é possível correlacionar o desempenho de qualquer consulta por meio do relatório de consulta. Este relatório compara o desempenho de consultas em bancos de dados diferentes e torna mais fácil identificar bancos de dados que executam a consulta selecionada em comparação com as lentas.

![Consultas de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

Você pode [criar alertas](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) facilmente com os dados provenientes de recursos de Banco de Dados SQL do Azure. Aqui estão algumas consultas de [pesquisa de logs](log-analytics-log-searches.md) que podem ser utilizadas com um alerta do log:

*CPU alta no Banco de Dados SQL do Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - O pré-requisito para configurar esse alerta é que os bancos de dados monitorados transmitam métricas de diagnóstico (opção "Todas as métricas") para a solução.
> - Substitua o valor cpu_percent de MetricName por dtu_consumption_percent para obter os resultados de DTU alta em vez disso.

*CPU alta em Pools Elásticos do Banco de Dados SQL do Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - O pré-requisito para configurar esse alerta é que os bancos de dados monitorados transmitam métricas de diagnóstico (opção "Todas as métricas") para a solução.
> - Substitua o valor cpu_percent de MetricName por dtu_consumption_percent para obter os resultados de DTU alta em vez disso.

*Banco de Dados SQL do Azure em média acima de 95% na última 1 hora*

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - O pré-requisito para configurar esse alerta é que os bancos de dados monitorados transmitam métricas de diagnóstico (opção "Todas as métricas") para a solução.
> - Essa consulta exige que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (resultados > 0) da consulta, indicando que a condição existe em alguns bancos de dados. A saída é uma lista de recursos de banco de dados que estão acima de storage_threshold dentro do time_range definido.
> - A saída é uma lista de recursos de banco de dados que estão acima de storage_threshold dentro do time_range definido.

*Alerta no Intelligent Insights*

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active" 
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - O pré-requisito para configurar esse alerta é que os bancos de dados monitorados transmitam log de diagnóstico do SQLInsights para a solução.
> - Essa consulta exige que uma regra de alerta seja configurada para ser executada com a mesma frequência que alert_run_interval a fim de evitar resultados duplicados. A regra deve ser configurada para disparar o alerta quando houver resultados (resultados > 0) da consulta.
> - Personalize o alert_run_interval para especificar o intervalo de tempo a ser verificado se a condição tiver ocorrido em bancos de dados configurados para transmitir log do SQLInsights à solução.
> - Personalize o insights_string para capturar a saída do texto da análise de causa de raiz do Insights. Este é o mesmo texto exibido na interface do usuário da solução, o qual você pode usar dos insights existentes. Como alternativa, você pode usar a consulta a seguir para ver o texto de todos os Insights gerados em sua assinatura. Use a saída da consulta para coletar as cadeias de caracteres distintas para configurar alertas no Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active" 
| distinct rootCauseAnalysis_s
```

## <a name="next-steps"></a>Próximas etapas

- Usar [Pesquisas de Log](log-analytics-log-searches.md) no Log Analytics para exibir dados detalhados do Azure SQL.
- [Criar seus próprios painéis](log-analytics-dashboards.md) mostrando os dados do Azure SQL.
- [Criar alertas](log-analytics-alerts.md) quando ocorrerem eventos específicos do Azure SQL.
