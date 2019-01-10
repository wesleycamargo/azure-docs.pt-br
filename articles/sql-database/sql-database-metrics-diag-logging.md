---
title: Métricas de banco de dados SQL do Azure e log de diagnósticos | Microsoft Docs
description: Saiba como configurar o Banco de Dados SQL do Azure para armazenar o uso de recursos e as estatísticas de execução de consulta.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 49c411487a29a7faa5a6cec5087a85d472309a4b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044562"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas de banco de dados SQL do Azure e o log de diagnóstico

Banco de dados SQL do Azure, pools elásticos, instância gerenciada e bancos de dados em podem de instância gerenciada transmitem métricas e logs de diagnóstico para facilitar o monitoramento de desempenho. Você pode configurar um banco de dados para transmitir o uso de recursos, trabalhos e sessões e conectividade para um dos seguintes recursos do Azure:

- **Análise de SQL do Azure**: para obter monitoramento inteligente de seus bancos de dados do Azure incluindo relatórios de desempenho, alertas e recomendações de mitigação.
- **Hubs de Eventos do Azure**: para integrar telemetria de Banco de Dados SQL com suas soluções de monitoramento personalizadas ou pipelines ativos.
- **Armazenamento do Microsoft Azure**: para arquivar grandes quantidades de telemetria por uma fração do preço.

    ![Arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

Para obter mais informações sobre as categorias de log e métricas com suporte a vários serviços do Azure, veja:

- [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Visão geral do log de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Este artigo apresenta diretrizes para ajudá-lo a habilitar a telemetria de diagnóstico para bancos de dados, pools elásticos e Instância Gerenciada. Ele também pode ajudar você entender como configurar a Análise de SQL do Azure como uma ferramenta de monitoramento para exibir a telemetria de diagnóstico do banco de dados.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Habilitar o log de telemetria de diagnóstico

É possível habilitar e gerenciar as métricas e o log de diagnósticos usando um dos seguintes métodos:

- Portal do Azure
- PowerShell
- CLI do Azure
- API REST do Azure Monitor
- Modelo do Azure Resource Manager

Quando você habilita o log e as métricas de diagnóstico, precisa especificar o destino de recursos do Azure para coletar a telemetria de diagnóstico. As opções disponíveis incluem:

- Azure SQL Analytics
- Hubs de eventos do Azure
- Armazenamento do Azure

Você pode provisionar um novo recurso do Azure ou selecionar um recurso existente. Depois de escolher um recurso usando a opção **Configurações de diagnóstico**, especifique quais dados coletar.

> [!NOTE]
> Se você também está usando pools elásticos ou Instância Gerenciada, é recomendável habilitar a telemetria de diagnóstico para esses recursos também. Contêineres de banco de dados em pools elásticos e a instância gerenciada têm a própria telemetria de diagnóstico separada.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Habilite o log de banco de dados SQL do Azure ou bancos de dados na instância gerenciada

Habilite as métricas e o registro em log de diagnósticos no Banco de Dados SQL e nos bancos de dados na Instância Gerenciada; elas não são habilitadas por padrão.

Você pode configurar Bancos de Dados SQL do Azure e bancos de dados na Instância Gerenciada para coletar a telemetria de diagnóstico a seguir:

| Telemetria de monitoramento para bancos de dados | Suporte para Banco de Dados SQL do Azure | Suporte para banco de dados na Instância Gerenciada |
| :------------------- | ------------------- | ------------------- |
| [Todas as métricas](sql-database-metrics-diag-logging.md#all-metrics): contém o percentual de DTU/CPU, o limite de DTU/CPU, o percentual de leitura de dados físicos, o percentual de gravação em log, êxito/falha/bloqueio por conexões de firewall, o percentual de sessões, o percentual de funcionários, o armazenamento, o percentual de armazenamento e o percentual de armazenamento XTP. | SIM | Não  |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): contém informações sobre as estatísticas de tempo de execução da consulta, como estatísticas de uso de CPU e duração da consulta. | SIM | SIM |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): contém informações sobre as estatísticas de espera de consulta (o que suas consultas aguardaram), como CPU, LOG e LOCKING. | SIM | SIM |
| [Errors](sql-database-metrics-diag-logging.md#errors-dataset): contém informações sobre erros de SQL no banco de dados. | SIM | Não  |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): contém informações sobre quanto tempo o banco de dados gastou aguardando em diferentes tipos de espera. | SIM | Não  |
| [Timeouts](sql-database-metrics-diag-logging.md#time-outs-dataset): contém informações sobre os tempos limite no banco de dados. | SIM | Não  |
| [Blocks](sql-database-metrics-diag-logging.md#blockings-dataset): contém informações sobre eventos de bloqueio no banco de dados. | SIM | Não  |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): contém Intelligent Insights sobre desempenho. Para saber mais, veja [Intelligent Insights](sql-database-intelligent-insights.md). | SIM | SIM |

### <a name="azure-portal"></a>Portal do Azure

Você usa o menu **Configurações de diagnóstico** para cada um dos bancos de dados no portal do Azure para configurar a transmissão de telemetria de diagnóstico para Bancos de Dados SQL do Azure e bancos de dados na Instância Gerenciada. Você pode definir os destinos a seguir: Armazenamento do Azure, Hubs de Eventos do Azure e Azure Log Analytics.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Configurar a transmissão de telemetria de diagnóstico para o Banco de Dados SQL do Microsoft Azure

   ![Ícone do Banco de Dados SQL](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Para habilitar o streaming de telemetria de diagnóstico para o Banco de Dados SQL do Azure, siga estas etapas:

1. Vá para o recurso de Banco de Dados SQL do Azure.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.
   - Você pode criar até três conexões paralelas para transmitir telemetria de diagnóstico.
   - Selecione **+Adicionar configuração de diagnóstico** para configurar o streaming de dados de diagnóstico para vários recursos.

   ![Habilitar o diagnóstico para o Banco de Dados SQL do Microsoft Azure](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar na conta de armazenamento**, **Transmitir para um hub de eventos** e **Enviar para Log Analytics**.
1. Para obter a experiência de monitoramento padrão baseada em eventos, marque as seguintes caixas de seleção para telemetria de log de diagnóstico do banco de dados: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks** e **Deadlocks**.
1. Para obter uma experiência de monitoramento avançada com base em um minuto, marque a caixa de seleção **AllMetrics**.
1. Clique em **Salvar**.

   ![Configura o diagnóstico para o Banco de Dados SQL do Microsoft Azure](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> Os logs de auditoria de segurança não podem ser habilitados nas configurações de diagnóstico do banco de dados. Para habilitar o streaming de log de auditoria, veja [Configurar a auditoria do banco de dados](sql-database-auditing.md#subheading-2) e também [Logs de auditoria do SQL no Azure Log Analytics e Hubs de Eventos do Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/).
> [!TIP]
> Repita essas etapas para cada Banco de Dados SQL do Azure que você deseja monitorar.

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Configurar a transmissão de telemetria de diagnóstico para bancos de dados na Instância Gerenciada do Banco de Dados SQL do Azure

   ![No ícone de instância gerenciada do banco de dados](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Para habilitar o streaming de telemetria de diagnóstico para bancos de dados na Instância Gerenciada do Banco de Dados SQL do Azure, siga estas etapas:

1. Vá para seu banco de dados na Instância Gerenciada.
2. Selecionar **configurações de Diagnóstico**.
3. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.
   - Você pode criar até três (3) conexões paralelas para transmitir telemetria de diagnóstico.
   - Selecione **+Adicionar configuração de diagnóstico** para configurar o streaming de dados de diagnóstico para vários recursos.

   ![Habilitar o diagnóstico para o banco de dados de instância gerenciada](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Insira um nome de configuração para sua própria referência.
5. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar na conta de armazenamento**, **Transmitir para um hub de eventos** e **Enviar para Log Analytics**.
6. Marque as caixas de seleção para a telemetria de diagnóstico do banco de dados: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** e **Errors**.
7. Clique em **Salvar**.

   ![Configurar o diagnóstico para o banco de dados de instância gerenciada](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> Repita essas etapas para cada banco de dados na Instância Gerenciada que você deseja monitorar.

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Habilitar registro em log para pool elástico ou Instâncias Gerenciadas

Habilite a telemetria de diagnóstico para pools elásticos e a Instância Gerenciada como contêineres de banco de dados. Eles têm a própria telemetria de diagnóstico que não está habilitada por padrão.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurar a transmissão de telemetria de diagnóstico para pools elásticos

   ![Ícone de pool Elástico](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Você pode configurar um recurso de pool elástico para coletar a telemetria de diagnóstico a seguir:

| Recurso | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Pool elástico** | [Todas as métricas](sql-database-metrics-diag-logging.md#all-metrics) contém o percentual de eDTU/CPU, o limite de eDTU/CPU, o percentual de leitura de dados físicos, o percentual de gravação de log, o percentual de sessões, o percentual de funcionários, o armazenamento, o percentual de armazenamento, o limite de armazenamento e o percentual de armazenamento XTP. |

Para habilitar o streaming de telemetria de diagnóstico para recursos de um pool elástico, siga estas etapas:

1. Vá para o recurso de pool Elástico no portal do Azure.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.

   ![Habilitar o diagnóstico para pools elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar na conta de armazenamento**, **Transmitir para um hub de eventos** e **Enviar para Log Analytics**.
1. Para Log Analytics, selecione **Configurar** e crie um workspace selecionando **+ Criar novo workspace** ou selecione um workspace.
1. Marque a caixa de seleção para telemetria de diagnóstico de pool elástico: **AllMetrics**.
1. Clique em **Salvar**.

   ![Configura o diagnóstico para pools elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> Repita essas etapas para cada pool elástico que você deseja monitorar.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Configurar a transmissão de telemetria de diagnóstico para a Instância Gerenciada

   ![Ícone da Instância Gerenciada](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Você pode configurar um recurso de Instância Gerenciada para coletar a telemetria de diagnóstico a seguir:

| Recurso | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Instância Gerenciada** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#logs-for-managed-instance) contém a contagem de vCores, o percentual médio de CPU, as solicitações de E/S, os bytes lidos/gravados, o espaço de armazenamento reservado e o espaço de armazenamento usado. |

Para habilitar o streaming de telemetria de diagnóstico para um recurso de Instância Gerenciada, siga estas etapas:

1. Vá para o recurso de Instância Gerenciada no portal do Azure.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.

   ![Habilitar o diagnóstico para a Instância Gerenciada do Banco de Dados SQL do Azure](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Selecione um recurso de destino para os dados de diagnóstico de streaming: **Arquivar na conta de armazenamento**, **Transmitir para um hub de eventos** e **Enviar para Log Analytics**.
1. Para Log Analytics, selecione **Configurar** e crie um workspace selecionando **+Criar Novo Workspace** ou use um workspace existente.
1. Marque a caixa de seleção para telemetria de diagnóstico de instância: **ResourceUsageStats**.
1. Clique em **Salvar**.

   ![Configurar o diagnóstico para Instância Gerenciada do Banco de Dados SQL do Azure](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!TIP]
> Repita essas etapas para cada Instância Gerenciada do Banco de Dados SQL do Azure que você deseja monitorar.

### <a name="powershell"></a>PowerShell

Você pode habilitar o log de diagnóstico e métricas usando o PowerShell.

- Para habilitar o armazenamento do log de diagnóstico em uma conta de armazenamento, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da conta de armazenamento é a ID do recurso para a conta de armazenamento de destino.

- Para habilitar o streaming dos logs de diagnóstico para um hub de eventos, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   A ID da regra do Barramento de Serviço do Azure é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar o envio de logs de diagnóstico para um workspace do Log Analytics, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- É possível obter a ID de recurso do workspace do Log Analytics usando o seguinte comando:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="to-configure-multiple-azure-resources"></a>Configurar vários recursos do Azure

Para dar suporte a várias assinaturas, use o script do PowerShell de [Habilitar log de métricas de recursos do Azure usando o PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Insira a ID de recurso do workspace \<$WSID\> como um parâmetro ao executar o script `Enable-AzureRMDiagnostics.ps1` para enviar dados de diagnóstico de diversos recursos para o workspace.

- Para obter a ID do workspace \<$WSID\> de destino para seus dados de diagnóstico, use o seguinte script:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   Substitua \<subID\> pela ID de assinatura \<RG_NAME\> com o nome do grupo de recursos e \<WS_NAME\> com o nome do workspace.

### <a name="azure-cli"></a>CLI do Azure

Você pode habilitar o log de diagnóstico e métricas usando a CLI do Azure.

- Para habilitar armazenamento do log de diagnóstico em uma conta de armazenamento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A ID da conta de armazenamento é a ID do recurso para a conta de armazenamento de destino.

- Para habilitar streaming dos logs de diagnóstico para um hub de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A ID da regra do Barramento de Serviço é uma cadeia de caracteres com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar envio dos logs de diagnóstico para um workspace do Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba como [alterar as configurações de diagnóstico usando a API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba como [habilitar as configurações de diagnóstico na criação de recursos usando um modelo do Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Stream em Análise de SQL do Azure

A Análise de SQL do Azure é uma solução de nuvem que monitora o desempenho de bancos de dados SQL do Azure, de pools elásticos e da Instância Gerenciada em escala e em várias assinaturas. Pode ajudá-lo a coletar e visualizar as métricas de desempenho do Banco de Dados SQL do Azure e tem uma inteligência interna para solução de problemas de desempenho.

![Visão geral da Análise de SQL do Azure](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Logs de diagnóstico e métricas de banco de dados SQL podem ser transmitidos para Análise de SQL do Azure usando o interno **enviar para Log Analytics** opção na guia de configurações de diagnóstico no portal. Também é possível habilitar o Log Analytics usando uma configuração de diagnóstico por meio dos cmdlets do PowerShell, da CLI do Azure ou da API REST do Azure Monitor.

### <a name="installation-overview"></a>Visão geral da instalação

Você pode monitorar uma frota de Bancos de Dados SQL com a Análise de SQL do Azure. Execute as seguintes etapas:

1. Criar uma solução de Análise de SQL do Azure usando o Azure Marketplace.
2. Criar workspace de monitoramento na solução.
3. Configurar bancos de dados para transmitir telemetria de diagnóstico para o workspace.

Se você estiver usando pools elásticos ou Instância Gerenciada, também precisará configurar a telemetria de diagnóstico de streaming desses recursos.

### <a name="create-azure-sql-analytics-resource"></a>Criar recurso de Análise de SQL do Azure

1. Pesquise a Análise de SQL do Azure no Azure Marketplace e selecione-o.

   ![Pesquisar para Análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Selecione **Criar** na tela de visão geral da solução.

3. Preencha o formulário de Análise de SQL do Azure com as informações adicionais necessárias: nome do workspace, assinatura, grupo de recursos, local e tipo de preço.

   ![Configurar Análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Selecione **OK** para confirmar e, em seguida, selecione **Criar**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurar bancos de dados para gravar logs de diagnóstico e métricas

A maneira mais fácil de configurar o local em que a métrica de registros de bancos de dados está é por meio do portal do Azure. Conforme descrito anteriormente, vá até o recurso de Banco de Dados SQL no portal do Azure e selecione **Configurações de diagnóstico**.

Se você estiver usando pools elásticos ou Instância Gerenciada, também precisará definir as configurações de diagnóstico nestes recursos para habilitar a telemetria de diagnóstico para transmitir para o workspace.

### <a name="use-the-sql-analytics-solution"></a>Use a solução Análise de SQL

Você pode usar a Análise do SQL como um painel hierárquico para exibir os recursos de Banco de Dados SQL. Para saber como usar a solução de Análise de SQL, consulte [Monitorar o Banco de Dados SQL usando a solução de Análise de SQL](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Transmitir para os Hubs de Eventos

Você pode transmitir métricas e logs de diagnóstico do Banco de Dados SQL para Hubs de Eventos usando a opção interna **Transmitir para um hub de eventos** no portal do Azure. Também é possível habilitar a ID de regra do Barramento de Serviço usando uma configuração de diagnóstico por meio dos cmdlets do PowerShell, da CLI do Azure ou da API REST do Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>O que fazer com as métricas e os logs de diagnóstico nos Hubs de Eventos

Depois que os dados selecionados são transmitidos para os Hubs de Eventos, os habilitar cenários de monitoramento avançado estarão mais próximos. Os Hubs de Eventos atuam como uma porta da frente para um pipeline de eventos. Depois que os dados são coletados para um hub de eventos, eles podem ser transformados e armazenados usando um provedor de análise em tempo real ou um adaptador de armazenamento. Os Hubs de Eventos separam a produção de um fluxo de eventos do consumo desses eventos. Dessa forma, os consumidores de evento podem acessá-los em suas próprias agendas. Para obter mais informações sobre os Hubs de Eventos, consulte:

- [O que são Hubs de Eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Você pode usar métricas de streaming em Hubs de Eventos para:

- **Exibir a integridade do serviço transmitindo dados de caminhos recorrentes ao Power BI**. Ao utilizar os Hubs de Eventos, o Stream Analytics e o Power BI, é fácil transformar suas métricas e dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Para obter uma visão geral de como configurar um hub de eventos, processar dados com o Stream Analytics e usar o Power BI como uma saída, consulte [Stream Analytics e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Transmitir logs para registros de terceiros e fluxos de telemetria**. Ao utilizar a transmissão dos Hubs de Eventos, é possível obter métricas e logs de diagnóstico em diversas soluções de monitoramento de terceiros e análise de logs.

- **Crie uma plataforma personalizada de registro em log e telemetria**. Fazer você já tem uma plataforma de telemetria personalizada ou está pensando em criar uma? A natureza altamente escalonável de publicação-assinatura de Hubs de Eventos permite flexibilidade para ingerir logs de diagnóstico. Consulte [o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Transmitir para o Armazenamento

Você pode armazenar métricas de Banco de Dados SQL e logs de diagnóstico no Armazenamento do Azure usando a opção interna **Arquivar em uma conta de armazenamento** no portal do Azure. Também é possível habilitar o Armazenamento usando uma configuração de diagnóstico por meio dos cmdlets do PowerShell, da CLI do Azure ou da API REST do Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Esquema de métricas e logs de diagnóstico na conta de armazenamento

Depois que você configurar a coleta de métricas e logs de diagnóstico, um contêiner de armazenamento é criado na conta de armazenamento selecionada quando as primeiras linhas de dados estão disponíveis. A estrutura dos blobs é:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ou, simplesmente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, um nome de blob para todas as métricas pode ser:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Um nome de blob para armazenar dados de um pool elástico se parece com:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Fazer download de métricas e logs do Armazenamento

Saiba como [baixar métricas e logs de diagnóstico do Armazenamento](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Política de retenção de dados e preços

Ao selecionar os Hubs de Eventos ou uma conta de Armazenamento, é possível especificar uma política de retenção. Essa política exclui dados mais antigos que um período de tempo selecionado. Se você especificar a análise de Log, a política de retenção depende do tipo de preço selecionado. Nesse caso, as unidades gratuitas de ingestão de dados fornecidas podem habilitar o monitoramento gratuito de vários bancos de dados por mês. Qualquer consumo de telemetria de diagnóstico que exceda as unidades gratuitas poderá gerar custos. Lembre-se de que os bancos de dados ativos com cargas de trabalho mais pesadas ingerem mais dados do que bancos de dados ociosos. Para saber mais, consulte [Preços do Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Se você estiver usando a Análise de SQL do Azure, poderá monitorar o consumo de ingestão de dados na solução selecionando o **Workspace OMS** no menu de navegação da Análise de SQL do Azure e, em seguida, selecionando **Uso** e **Custos Estimados**.

## <a name="metrics-and-logs-available"></a>Métricas e logs disponíveis

Telemetria de monitoramento coletada pode ser usada para seu próprio _análises personalizadas_ e _desenvolvimento de aplicativos_ usando [linguagem SQL Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="all-metrics"></a>Todas as métricas

Consulte as tabelas a seguir para obter detalhes sobre todas as métricas por recurso.

### <a name="all-metrics-for-elastic-pools"></a>Todas as métricas para pools elásticos

|**Recurso**|**Métricas**|
|---|---|
|Pool elástico|O percentual de eDTU, eDTU usado, limite de eDTU, percentual de CPU, percentual de leitura de dados físicos, percentual de gravação de log, percentual de sessões, percentual de funcionários, armazenamento, percentual de armazenamento, limite de armazenamento, percentual de armazenamento XTP |

### <a name="all-metrics-for-azure-sql-databases"></a>Todas as métricas para o Banco de Dados SQL do Azure

|**Recurso**|**Métricas**|
|---|---|
|Banco de Dados SQL Azure|O percentual de DTU, DTU usado, o limite de DTU, percentual de CPU, percentual de leitura de dados físicos, percentual de gravação de log, êxito/falha/bloqueio por conexões de firewall, percentual de sessões, percentual de funcionários, armazenamento, percentual de armazenamento, percentual de armazenamento XTP e deadlocks |

## <a name="logs-for-managed-instance"></a>Logs para a Instância Gerenciada

Veja a tabela a seguir para obter detalhes sobre os logs para a Instância Gerenciada.

### <a name="resource-usage-statistics"></a>Estatísticas de uso de recursos

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: ResourceUsageStats |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: MANAGEDINSTANCES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome da Instância Gerenciada |
|ResourceId|URI de recurso |
|SKU_s|SKU do produto de Instância Gerenciada |
|virtual_core_count_s|Número de vCores disponíveis |
|avg_cpu_percent_s|Percentual médio de CPU |
|reserved_storage_mb_s|Capacidade de armazenamento reservada na Instância Gerenciada |
|storage_space_used_mb_s|Armazenamento usado na Instância Gerenciada |
|io_requests_s|Contagem de IOPS |
|io_bytes_read_s|Bytes de IOPS lidos |
|io_bytes_written_s|Bytes de IOPS gravados |

## <a name="logs-for-azure-sql-databases-and-managed-instance-databases"></a>Logs do Banco de Dados SQL do Azure e do banco de dados da Instância Gerenciada

Consulte as tabelas a seguir para obter detalhes sobre os logs para Bancos de Dados SQL do Azure e Instância Gerenciada.

### <a name="query-store-runtime-statistics"></a>Estatísticas de tempo de execução do Repositório de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics |
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|query_hash_s|Hash de consulta |
|query_plan_hash_s|Hash do plano de consulta |
|statement_sql_handle_s|Identificador de sql da instrução |
|interval_start_time_d|Inicie o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|interval_end_time_d|Encerre o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|logical_io_writes_d|Número total de gravações lógicas de E/S |
|max_logical_io_writes_d|Número máximo de gravações lógicas de E/S por execução |
|physical_io_reads_d|Número total de leituras físicas de E/S |
|max_physical_io_reads_d|Número máximo de leituras lógicas de E/S por execução |
|logical_io_reads_d|Número total de leituras lógicas de E/S |
|max_logical_io_reads_d|Número máximo de leituras lógicas de E/S por execução |
|execution_type_d|Tipo de execução |
|count_executions_d|Número de execuções da consulta |
|cpu_time_d|Tempo total de CPU consumido pela consulta em microssegundos |
|max_cpu_time_d|Tempo máximo de CPU do consumidor por uma execução única em microssegundos |
|dop_d|Soma de graus de paralelismo |
|max_dop_d|Grau máximo de paralelismo usado para execução única |
|rowcount_d|Número total de linhas retornadas |
|max_rowcount_d|Número máximo de linhas retornadas em uma única execução |
|query_max_used_memory_d|Quantidade total de memória usada em KB |
|max_query_max_used_memory_d|Quantidade máxima de memória usada por uma única execução em KB |
|duration_d|Tempo total de execução em microssegundos |
|max_duration_d|Tempo máximo de execução de uma única execução |
|num_physical_io_reads_d|Número total de leituras físicas |
|max_num_physical_io_reads_d|Número máximo de leituras físicas por execução |
|log_bytes_used_d|Quantidade total de bytes de log usados |
|max_log_bytes_used_d|Quantidade máxima de bytes de log usados por execução |
|query_id_d|ID da consulta no Repositório de Consultas |
|plan_id_d|ID do plano no Repositório de Consultas |

Saiba mais sobre os [Dados de estatísticas de tempo de execução do Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Estatísticas de espera do Repositório de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreWaitStatistics |
|OperationName|Nome da operação. Sempre: QueryStoreWaitStatisticsEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|wait_category_s|Categoria da espera |
|is_parameterizable_s|É a consulta parametrizável |
|statement_type_s|Tipo da instrução |
|statement_key_hash_s|Hash de chave de instrução |
|exec_type_d|Tipo de execução |
|total_query_wait_time_ms_d|Tempo total de espera da consulta na categoria de espera específica |
|max_query_wait_time_ms_d|Tempo máximo de espera da consulta em execução individual na categoria de espera específica |
|query_param_type_d|0 |
|query_hash_s|Hash de consulta no Repositório de Consultas |
|query_plan_hash_s|Hash de plano de consulta no Repositório de consultas |
|statement_sql_handle_s|Identificador de instrução no Repositório de consultas |
|interval_start_time_d|Inicie o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|interval_end_time_d|Encerre o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|count_executions_d|Contagem de execuções da consulta |
|query_id_d|ID da consulta no Repositório de Consultas |
|plan_id_d|ID do plano no Repositório de Consultas |

Saiba mais sobre os [Dados de estatísticas de espera no Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Conjunto de dados de erros

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQ |
|Categoria|Nome da categoria. Sempre: Errors |
|OperationName|Nome da operação. Sempre: ErrorEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|Mensagem|Mensagem de erro em texto sem formatação |
|user_defined_b|É o bit de erro definido pelo usuário |
|error_number_d|Código do erro |
|Severity|Gravidade do erro |
|state_d|Estado do erro |
|query_hash_s|Hash de consulta da consulta com falha, se disponível |
|query_plan_hash_s|Hash do plano de consulta da consulta com falha, se disponível |

Saiba mais sobre as [Mensagens de erro do SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Conjunto de dados das estatísticas de espera do banco de dados

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: DatabaseWaitStatistics |
|OperationName|Nome da operação. Sempre: DatabaseWaitStatisticsEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|wait_type_s|Nome do tipo de espera |
|start_utc_date_t [UTC]|Hora de início do período de medida |
|end_utc_date_t [UTC]|Hora de término do período de medida |
|delta_max_wait_time_ms_d|Tempo máximo de espera por execução |
|delta_signal_wait_time_ms_d|Tempo de espera total de sinais |
|delta_wait_time_ms_d|Tempo total de espera no período |
|delta_waiting_tasks_count_d|Número de tarefas em espera |

Saiba mais sobre as [estatísticas de espera no banco de dados](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Conjunto de dados de tempos limite

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Tempos limite |
|OperationName|Nome da operação. Sempre: TimeoutEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|error_state_d|Código do estado de erro |
|query_hash_s|Hash de consulta, se disponível |
|query_plan_hash_s|Hash do plano de consulta, se disponível |

### <a name="blockings-dataset"></a>Conjunto de dados de boqueios

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Bloqueios |
|OperationName|Nome da operação. Sempre: BlockEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|lock_mode_s|Modo de bloqueio usado pela consulta |
|resource_owner_type_s|Proprietário do bloqueio |
|blocked_process_filtered_s|XML de relatório de processo bloqueado |
|duration_d|Duração do bloqueio em microssegundos |

### <a name="deadlocks-dataset"></a>Conjunto de dados de deadlocks

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC] |Carimbo de data/hora de quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Deadlocks |
|OperationName|Nome da operação. Sempre: DeadlockEvent |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|deadlock_xml_s|Relatório de deadlock XML |

### <a name="automatic-tuning-dataset"></a>Conjunto de dados de ajuste automático

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Tipo|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: AutomaticTuning |
|Recurso|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVERS/DATABASES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|LogicalDatabaseName_s|Nome do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|RecommendationHash_s|Hash exclusivo de recomendação de ajuste automático |
|OptionName_s|Opções de ajuste automático |
|Schema_s|Esquema de banco de dados |
|Table_s|Tabela afetada |
|IndexName_s|Nome do índice |
|IndexColumns_s|Nome da coluna |
|IncludedColumns_s|Colunas incluídas |
|EstimatedImpact_s|Estimado impacto recomendação de ajuste automático JSON |
|Event_s|Tipo de evento de ajuste automático |
|Timestamp_t|Última atualização de carimbo de data/hora |

### <a name="intelligent-insights-dataset"></a>Conjunto de dados do Insights inteligentes

Saiba mais sobre o [formato de log do Insights Inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Próximas etapas

Para saber como habilitar o registro em log e entender as categorias de métrica e log com suporte dos vários serviços do Azure, veja:

- [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Visão geral do log de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Para saber mais sobre os Hubs de Evento, leia:

- [O que é Hub de Eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Para saber mais sobre o Armazenamento do Azure, veja como [baixar métricas e logs de diagnóstico do Armazenamento](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
