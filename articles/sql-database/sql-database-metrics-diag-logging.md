---
title: Métricas de banco de dados SQL do Azure e log de diagnósticos | Microsoft Docs
description: Saiba mais sobre como configurar o Banco de Dados SQL do Azure para armazenar o uso de recursos, a conectividade e as estatísticas de execução de consulta.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: b903d0ddbccac8fe4fa8b251d409bd8addebb435
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425992"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas de banco de dados SQL do Azure e o log de diagnóstico 

Banco de dados SQL do Azure, pools elásticos, instância gerenciada e bancos de dados em podem de instância gerenciada emitem métricas e logs de diagnóstico para facilitar o monitoramento de desempenho. Você pode configurar um banco de dados para transmitir dados de uso de recursos, trabalhos, sessões e conectividade para um destes recursos do Azure:

* **Análise de SQL do Azure**: usado como uma solução de monitoramento de desempenho inteligente de banco de dados do Azure com recursos de relatórios, alertas e atenuação.
* **Hubs de Eventos do Azure**: para a integração de telemetria do Banco de Dados SQL com a sua solução de monitoramento personalizada ou pipelines ativos.
* **Armazenamento do Microsoft Azure**: usado para arquivamento de grandes quantidades de telemetria por uma fração do preço.

    ![Arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

Para entender as métricas e categorias com suporte a vários serviços do Azure de log, você talvez queira pensar em ler:

* [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Visão geral do log de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

## <a name="enable-logging-of-diagnostics-telemetry"></a>Habilitar o log de telemetria de diagnóstico

Use a primeira seção deste documento para habilitar a telemetria de diagnóstico para bancos de dados e a segunda parte do documento para habilitar a telemetria de diagnóstico para pools elásticos ou instâncias gerenciadas. Use as seções posteriores deste documento para configurar a Análise de SQL do Azure como uma ferramenta de monitoramento para exibição da telemetria de diagnóstico do banco de dados em fluxo.

> [!NOTE]
> Caso você esteja usando pools elásticos ou instâncias gerenciadas, além de habilitar a telemetria de diagnóstico para bancos de dados, também é recomendável habilitar a telemetria de diagnóstico para esses recursos também. Isso ocorre porque pool elástico e instâncias gerenciadas na função de contêineres de banco de dados têm sua própria telemetria de diagnóstico que é separada da telemetria de diagnóstico banco de dados individuais. 
>

É possível habilitar e gerenciar as métricas e o log de diagnósticos usando um dos seguintes métodos:

- Portal do Azure
- PowerShell
- CLI do Azure
- API REST do Azure Monitor 
- Modelo do Azure Resource Manager

Quando você habilita o log de diagnóstico e métricas, você precisa especificar o destino de recursos do Azure onde os dados selecionados serão coletados. As opções disponíveis incluem:

- Azure SQL Analytics
- Hubs de eventos do Azure
- Armazenamento do Azure

Você pode provisionar um novo recurso do Azure ou selecionar um recurso existente. Depois de selecionar um recurso, usando a opção de configurações de diagnóstico, você precisa especificar quais dados coletar.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Habilite o log de banco de dados SQL do Azure ou bancos de dados na instância gerenciada

As métricas e log de diagnósticos no banco de dados SQL e bancos de dados na Instância Gerenciada do Banco de Dados SQL do Azure não estão habilitados por padrão.

A telemetria de diagnóstico a seguir está disponível para a coleção de bancos de dados SQL do Azure e bancos de dados na instância gerenciada:

| Telemetria de monitoramento para bancos de dados | Suporte para Banco de Dados SQL do Azure | Suporte para banco de dados na Instância Gerenciada |
| :------------------- | ------------------- | ------------------- |
| [Todas as métricas](sql-database-metrics-diag-logging.md#all-metrics): contém o percentual de DTU/CPU, o limite de DTU/CPU, o percentual de leitura de dados físicos, o percentual de gravação em log, êxito/falha/bloqueio por conexões de firewall, o percentual de sessões, o percentual de funcionários, o armazenamento, o percentual de armazenamento e o percentual de armazenamento XTP. | SIM | Não  |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): contém informações sobre as estatísticas de tempo de execução da consulta, como o uso de CPU e estatísticas de duração da consulta. | SIM | SIM |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): contém informações sobre as estatísticas de espera da consulta, que informam sobre o que suas consultas aguardaram, como CPU, LOG e LOCKING. | SIM | SIM |
| [Erros](sql-database-metrics-diag-logging.md#errors-dataset): contém informações sobre erros de SQL que ocorreram neste banco de dados. | SIM | Não  |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): contém informações sobre a quantidade de tempo de espera de um banco de dados em diferentes tipos de espera. | SIM | Não  |
| [Tempos limite](sql-database-metrics-diag-logging.md#time-outs-dataset): contêm informações sobre os tempos limite ocorridos em um banco de dados. | SIM | Não  |
| [Bloqueios](sql-database-metrics-diag-logging.md#blockings-dataset): contém informações sobre eventos de bloqueio ocorridos em um banco de dados. | SIM | Não  |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): contém Intelligent Insights sobre o desempenho. [Saiba mais sobre Insights Inteligentes](sql-database-intelligent-insights.md). | SIM | SIM |

### <a name="azure-portal"></a>Portal do Azure

Streaming de telemetria de diagnóstico para o banco de dados SQL e bancos de dados na instância gerenciada para destinos de armazenamento do Azure, hubs de eventos ou Log Analytics é configurado por meio do menu de configurações de diagnóstico para cada um dos bancos de dados no portal do Azure.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Configurar a transmissão de telemetria de diagnóstico para o Banco de Dados SQL do Microsoft Azure

   ![Ícone do Banco de Dados SQL](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Para habilitar o streaming de telemetria de diagnóstico para o **Banco de Dados SQL do Microsoft Azure**, siga estas etapas:

1. Vá para o recurso de Banco de Dados SQL do Microsoft Azure
2. Selecione **configurações de Diagnóstico**
3. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir, ou selecione **Editar configuração** para editar uma configuração anterior
- Até três (3) conexões paralelas a telemetria de diagnóstico de fluxo podem ser criadas. Para configurar vários streamings paralelos de dados de diagnóstico para vários recursos, selecione **+ Adicionar configuração de diagnóstico** para criar uma configuração adicional.

   ![Habilitar o diagnóstico para o Banco de Dados SQL do Microsoft Azure](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

4. Digite o nome para a configuração – para sua própria referência
5. Selecione a qual recurso transmitir dados de diagnóstico do banco de dados: **Arquivo para a conta de armazenamento**, **Stream para um hub de eventos**, ou **enviar para Log Analytics**
6. Para a experiência de monitoramento padrão, marque as caixas de seleção para telemetria de logs de diagnóstico do banco de dados: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Erros**, **DatabaseWaitStatistics**, **Tempos limite**, **Blocos** , **Deadlocks**. Essa telemetria é o evento com base e fornece a experiência de monitoramento padrão.
7. Para a experiência de monitoramento avançada, marque a caixa de seleção **AllMetrics**. Isso é uma telemetria de com base em 1 minuto para a telemetria de diagnóstico do banco de dados, conforme descrito acima. 
8. Clique em **Salvar**

   ![Configura o diagnóstico para o Banco de Dados SQL do Microsoft Azure](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> Os logs de auditoria de segurança não podem ser habilitados nas configurações de diagnóstico do banco de dados. Para habilitar o streaming de log de auditoria, consulte [Configurar a auditoria do banco de dados](sql-database-auditing.md#subheading-2)e também ver [logs de auditoria do SQL no Azure Log Analytics e Hubs de Eventos do Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/).
>

> [!TIP]
> Repita as etapas acima para cada banco de dados do SQL do Azure você deseja monitorar. 
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Configurar a transmissão de telemetria de diagnóstico para bancos de dados na Instância Gerenciada do Banco de Dados SQL do Azure

   ![No ícone de instância gerenciada do banco de dados](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Para habilitar o streaming de telemetria de diagnóstico para **Bancos de dados na Instância Gerenciada do Banco de Dados SQL do Azure**, siga estas etapas:

1. Vá para seu banco de dados na instância gerenciada
2. Selecione **configurações de Diagnóstico**
3. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir, ou selecione **Editar configuração** para editar uma configuração anterior
- Até três (3) conexões paralelas a telemetria de diagnóstico de fluxo podem ser criadas. Para configurar vários streamings paralelos de dados de diagnóstico para vários recursos, selecione **+ Adicionar configuração de diagnóstico** para criar uma configuração adicional.

   ![Habilitar o diagnóstico para o banco de dados de instância gerenciada](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Digite o nome para a configuração – para sua própria referência
5. Selecione a qual recurso transmitir dados de diagnóstico do banco de dados: **Arquivo para a conta de armazenamento**, **Stream para um hub de eventos**, ou **enviar para Log Analytics**
6. Selecione as caixas de seleção para a telemetria de diagnóstico do banco de dados: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** e **Erros**
7. Clique em **Salvar**

   ![Configurar o diagnóstico para o banco de dados de instância gerenciada](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> Repita as etapas acima para cada banco de dados na instância gerenciada que você deseja monitorar.
>

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Habilitar registro em log para pool elástico ou Instâncias Gerenciadas

Pools elásticos e instâncias gerenciadas como contêineres de banco de dados têm sua própria telemetria de diagnóstico separada de bancos de dados. Essa telemetria de diagnóstico não está habilitada por padrão. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurar a transmissão de telemetria de diagnóstico para pools elásticos

   ![Ícone de pool Elástico](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

A telemetria de diagnóstico a seguir está disponível para a coleção para recurso de pool elástico:

| Recurso | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Pool elástico** | [Todas as métricas](sql-database-metrics-diag-logging.md#all-metrics) contém o percentual de eDTU/CPU, o limite de eDTU/CPU, o percentual de leitura de dados físicos, o percentual de gravação de log, o percentual de sessões, o percentual de funcionários, o armazenamento, o percentual de armazenamento, o limite de armazenamento e o percentual de armazenamento XTP. |

Para habilitar o streaming de telemetria de diagnóstico para **recursos do pool Elástico**, siga estas etapas:

1. Vá para o recurso de pool Elástico no portal do Azure
2. Selecione **configurações de Diagnóstico**
3. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir, ou selecione **Editar configuração** para editar uma configuração anterior

   ![Habilitar o diagnóstico para pools elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Digite o nome para a configuração – para sua própria referência
5. Selecione a qual recurso transmitir dados de diagnóstico do pool Elástico: **Arquivo para a conta de armazenamento**, **Stream para um hub de eventos**, ou **Enviar para o Log Analytics**
6. No caso do Log Analytics está selecionado, selecione **Configurar** e crie um novo espaço de trabalho selecionando **+ Criar novo espaço de trabalho**, ou selecione um espaço de trabalho
7. Marque a caixa de seleção para telemetria de diagnóstico de pool elástico **AllMetrics**
8. Clique em **Salvar**

   ![Configura o diagnóstico para pools elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> Repita as etapas acima para cada pool elástico que você deseja monitorar.
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Configurar a transmissão de telemetria de diagnóstico para a Instância Gerenciada

   ![Ícone da Instância Gerenciada](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

A telemetria de diagnóstico a seguir está disponível para a coleção para recurso de Instância Gerenciada do Banco de Dados SQL do Azure:

| Recurso | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Instância Gerenciada** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats) contém a contagem de vCores, o percentual médio de CPU, as solicitações de E/S, os bytes lidos/gravados, o espaço de armazenamento reservado e o espaço de armazenamento usado. |

Para habilitar o streaming de telemetria de diagnóstico para **Recurso de Instância Gerenciada**, siga estas etapas:

1. Vá para o recurso de instância gerenciada no portal do Azure
2. Selecione **configurações de Diagnóstico**
3. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir, ou selecione **Editar configuração** para editar uma configuração anterior

   ![Habilitar o diagnóstico para a Instância Gerenciada do Banco de Dados SQL do Azure](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Digite o nome para a configuração – para sua própria referência
5. Selecione a qual recurso transmitir dados de diagnóstico do pool Elástico: **Arquivo para a conta de armazenamento**, **Stream para um hub de eventos**, ou **Enviar para o Log Analytics**
6. No caso de o Log Analytics estar selecionado, crie ou use um espaço de trabalho
7. Marque a caixa de seleção por exemplo telemetria de diagnóstico **ResourceUsageStats**
8. Clique em **Salvar**

   ![Configurar o diagnóstico para Instância Gerenciada do Banco de Dados SQL do Azure](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!TIP]
> Repita as etapas acima para cada Instância Gerenciada do Banco de Dados SQL do Azure que você deseja monitorar.
>

### <a name="powershell"></a>PowerShell

Para habilitar as métricas e o log de diagnósticos usando o PowerShell, use os seguintes comandos:

- Para habilitar o armazenamento do log de diagnóstico em uma conta de armazenamento, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da conta de armazenamento é a ID de recurso da conta de armazenamento para a qual os logs serão enviados.

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

Insira a ID de recurso do workspace &lt;$WSID&gt; como um parâmetro ao executar o script (Enable-AzureRMDiagnostics.ps1), a fim de enviar dados de diagnóstico de diversos recursos para o workspace. Para obter a ID do workspace &lt;$WSID&gt; ao qual os dados de diagnóstico serão enviados, substitua &lt;subID&gt; pela ID de assinatura, &lt;RG_NAME&gt; pelo nome do grupo de recursos e &lt;WS_NAME&gt; pelo nome do workspace no script a seguir.

- Para configurar vários recursos do Azure, use os seguintes comandos:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>CLI do Azure

Para habilitar as métricas e o log de diagnóstico usando a CLI do Azure, use os seguintes comandos:

- Para habilitar o armazenamento do log de diagnóstico em uma conta de armazenamento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A ID da conta de armazenamento é a ID de recurso da conta de armazenamento para a qual os logs serão enviados.

- Para habilitar o streaming dos logs de diagnóstico para um hub de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A ID da regra do Barramento de Serviço é uma cadeia de caracteres com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar o envio de logs de diagnóstico para um workspace do Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba como [alterar as configurações de diagnóstico usando a API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba como [habilitar as configurações de diagnóstico na criação de recursos usando um modelo do Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-azure-sql-analytics"></a>Stream em Análise de SQL do Azure 

A Análise de SQL do Azure é uma solução de monitoramento em nuvem para monitorar o desempenho de bancos de dados SQL do Azure, pools elásticos e Instâncias Gerenciadas em escala e em várias assinaturas através de um único painel de vidro. Ela coleta e visualiza importantes métricas de desempenho do Banco de Dados SQL do Azure com inteligência interna para solução de problemas de desempenho.

![Visão geral da Análise de SQL do Azure](../log-analytics/media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Logs de diagnóstico e métricas de banco de dados SQL podem ser transmitidos para Análise de SQL do Azure usando o interno **enviar para Log Analytics** opção na folha de configurações de diagnóstico no portal. Também é possível habilitar o Log Analytics usando uma configuração de diagnóstico por meio dos cmdlets do PowerShell, da CLI do Azure ou da API REST do Azure Monitor.

### <a name="installation-overview"></a>Visão geral da instalação

Monitorar um fleet de banco de dados SQL é simples com Análise de SQL do Azure. Três etapas são necessárias:

1. Instalar a solução de Análise de SQL do Azure do Azure Marketplace
2. Criar espaço de trabalho de monitoramento na solução
3. Configure bancos de dados de telemetria de diagnóstico de fluxo no espaço de trabalho que você criou.

Caso você esteja usando pools elásticos ou instâncias gerenciadas, além de configurar a telemetria de diagnóstico do banco de dados, configure a transmissão de telemetria de diagnóstico desses recursos também.

### <a name="create-azure-sql-analytics-resource"></a>Criar recurso de Análise de SQL do Azure

1. Pesquise a Análise de SQL do Azure no Azure Marketplace e selecione-o

   ![Pesquisar para Análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)
   
2. Selecione **Criar** na tela de visão geral da solução

3. Preencha o formulário de Análise de SQL do Azure com as informações adicionais necessárias: nome do workspace, assinatura, grupo de recursos, local e tipo de preço.
 
   ![Configurar Análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Confirme marcando **OK**e finalize selecionando **Criar**

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurar bancos de dados para gravar logs de diagnóstico e métricas

A maneira mais fácil de configurar onde os bancos de dados registram suas métricas é por meio do portal do Azure - conforme descrito acima. No portal, acesse o recurso de Banco de Dados SQL e selecione **Configurações de diagnóstico**.

No caso de você estar usando pools elásticos ou instâncias gerenciadas, você também precisará definir as configurações de diagnóstico nesses recursos, bem como para transmitir sua própria telemetria de diagnóstico para o espaço de trabalho que você criou.

### <a name="use-the-sql-analytics-solution"></a>Use a solução Análise de SQL

A Análise de SQL é um painel hierárquico que permite navegar pela hierarquia de recursos do Banco de Dados SQL. Para saber como usar a solução de Análise de SQL, consulte [Monitorar o Banco de Dados SQL usando a solução de Análise de SQL](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Transmitir para os Hubs de Eventos

As métricas e os logs de diagnóstico do Banco de Dados SQL podem ser transmitidos para os Hubs de Eventos usando a opção interna **Transmitir para um hub de eventos** no portal. Também é possível habilitar a ID de regra do Barramento de Serviço usando uma configuração de diagnóstico por meio dos cmdlets do PowerShell, da CLI do Azure ou da API REST do Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>O que fazer com as métricas e os logs de diagnóstico nos Hubs de Eventos
Depois que os dados selecionados são transmitidos para os Hubs de Eventos, os habilitar cenários de monitoramento avançado estarão mais próximos. Os Hubs de Eventos atuam como uma porta da frente para um pipeline de eventos. Depois de enviados para um hub de eventos, os dados podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Os Hubs de Eventos separam a produção de um fluxo de eventos do consumo desses eventos. Dessa forma, os consumidores de evento podem acessá-los em suas próprias agendas. Para obter mais informações sobre os Hubs de Eventos, consulte:

- [O que são Hubs de Eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Veja algumas maneiras de usar a funcionalidade de streaming:

* **Exibir a integridade do serviço transmitindo dados de caminhos recorrentes ao Power BI**. Ao utilizar os Hubs de Eventos, o Stream Analytics e o Power BI, é fácil transformar suas métricas e dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Para obter uma visão geral de como configurar um hub de eventos, processar dados com o Stream Analytics e usar o Power BI como uma saída, consulte [Stream Analytics e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Transmitir logs para registros de terceiros e fluxos de telemetria**. Ao utilizar a transmissão dos Hubs de Eventos, é possível obter métricas e logs de diagnóstico em diferentes soluções de monitoramento de terceiros e análise de logs. 

* **Crie uma plataforma personalizada de registro em log e telemetria**. Se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos oferecerá flexibilidade na ingestão de logs de diagnóstico. Consulte [o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Transmitir para o Armazenamento

As métricas e os logs de diagnóstico do Banco de Dados SQL podem ser armazenados no Armazenamento usando a opção interna **Arquivar em uma conta de armazenamento** no portal. Também é possível habilitar o Armazenamento usando uma configuração de diagnóstico por meio dos cmdlets do PowerShell, da CLI do Azure ou da API REST do Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Esquema de métricas e logs de diagnóstico na conta de armazenamento

Depois que você configurar a coleta de métricas e logs de diagnóstico, um contêiner de armazenamento é criado na conta de armazenamento selecionada quando as primeiras linhas de dados estão disponíveis. A estrutura desses blobs é:

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

Caso você queira registrar os dados do pool elástico, o nome do blob é um pouco diferente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Fazer download de métricas e logs do Armazenamento

Saiba como [baixar métricas e logs de diagnóstico do Armazenamento](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Política de retenção de dados e preços

Ao selecionar os Hubs de Eventos ou uma conta de armazenamento, é possível especificar uma política de retenção. Essa política exclui dados mais antigos que um período de tempo selecionado. Se você especificar a análise de Log, a política de retenção depende do tipo de preço selecionado. Aplica-se o consumo de telemetria de diagnóstico acima das unidades livres de ingestão de dados alocada a cada mês. As unidades gratuitas de ingestão de dados fornecidas habilitam o monitoramento gratuito de vários bancos de dados por mês. Observe que os bancos de dados mais ativos, com cargas de trabalho mais pesadas, vão ingerir mais dados em comparação com os bancos de dados ociosos. Para saber mais, consulte [Preços do Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). 

Se você estiver usando A Análise de SQL do Azure, você pode monitorar facilmente o consumo de ingestão de dados na solução selecionando o espaço de trabalho do OMS no menu de navegação da Análise de SQL do Azure e, em seguida, selecionando Uso e custos estimados.

## <a name="metrics-and-logs-available"></a>Métricas e logs disponíveis

Telemetria de monitoramento coletada pode ser usada para seu próprio **análises personalizadas** e **desenvolvimento de aplicativos** usando [linguagem SQL Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries). A estrutura dos dados coletados, métricas e logs, está listada abaixo.

## <a name="all-metrics"></a>Todas as métricas

### <a name="all-metrics-for-elastic-pools"></a>Todas as métricas para pools elásticos

|**Recurso**|**Métricas**|
|---|---|
|Pool elástico|O percentual de eDTU, eDTU usado, limite de eDTU, percentual de CPU, percentual de leitura de dados físicos, percentual de gravação de log, percentual de sessões, percentual de funcionários, armazenamento, percentual de armazenamento, limite de armazenamento, percentual de armazenamento XTP |

### <a name="all-metrics-for-azure-sql-database"></a>Todas as métricas para o Banco de Dados SQL do Azure

|**Recurso**|**Métricas**|
|---|---|
|Banco de Dados SQL do Azure|O percentual de DTU, DTU usado, o limite de DTU, percentual de CPU, percentual de leitura de dados físicos, percentual de gravação de log, êxito/falha/bloqueio por conexões de firewall, percentual de sessões, percentual de funcionários, armazenamento, percentual de armazenamento, percentual de armazenamento XTP e deadlocks |

## <a name="logs"></a>Logs

### <a name="logs-for-managed-instance"></a>Logs para a Instância Gerenciada

### <a name="resource-usage-stats"></a>Estatísticas de uso do recurso

|Propriedade|Descrição|
|---|---|
|TenantId|ID do locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: ResourceUsageStats|
|Recurso|Nome do recurso.|
|ResourceType|Nome do tipo de recurso. Sempre: MANAGEDINSTANCES|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome da Instância Gerenciada.|
|ResourceId|URI de recurso.|
|SKU_s|SKU do produto de Instância Gerenciada|
|virtual_core_count_s|Número de vCores disponíveis|
|avg_cpu_percent_s|Percentual médio de CPU|
|reserved_storage_mb_s|Capacidade de armazenamento reservada na Instância Gerenciada|
|storage_space_used_mb_s|Armazenamento usado na Instância Gerenciada|
|io_requests_s|Contagem de IOPS|
|io_bytes_read_s|Bytes de IOPS lidos|
|io_bytes_written_s|Bytes de IOPS gravados|

### <a name="logs-for-azure-sql-database-and-managed-instance-database"></a>Logs do Banco de Dados SQL do Azure e do banco de dados da Instância Gerenciada

### <a name="query-store-runtime-statistics"></a>Estatísticas de tempo de execução do Repositório de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|ID do locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics|
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent|
|Recurso|Nome do recurso.|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados.|
|ResourceId|URI de recurso.|
|query_hash_s|Query hash.|
|query_plan_hash_s|Hash do plano de consulta.|
|statement_sql_handle_s|Identificador de sql da instrução.|
|interval_start_time_d|Inicie o datetimeoffset do intervalo em número de tiques de 1900-1-1.|
|interval_end_time_d|Encerre o datetimeoffset do intervalo em número de tiques de 1900-1-1.|
|logical_io_writes_d|Número total de gravações lógicas de E/S.|
|max_logical_io_writes_d|Número máximo de gravações lógicas de E/S por execução.|
|physical_io_reads_d|Número total de leituras físicas de E/S.|
|max_physical_io_reads_d|Número máximo de leituras lógicas de E/S por execução.|
|logical_io_reads_d|Número total de leituras lógicas de E/S.|
|max_logical_io_reads_d|Número máximo de leituras lógicas de E/S por execução.|
|execution_type_d|Tipo de execução.|
|count_executions_d|Número de execuções da consulta.|
|cpu_time_d|Tempo total de CPU consumido pela consulta em microssegundos.|
|max_cpu_time_d|Tempo máximo de CPU do consumidor por uma execução única em microssegundos.|
|dop_d|Soma de graus de paralelismo.|
|max_dop_d|Grau máximo de paralelismo usado para execução única.|
|rowcount_d|Número total de linhas retornadas.|
|max_rowcount_d|Número máximo de linhas retornadas em uma única execução.|
|query_max_used_memory_d|Quantidade total de memória usada em KB.|
|max_query_max_used_memory_d|Quantidade máxima de memória usada por uma única execução em KB.|
|duration_d|Tempo total de execução em microssegundos.|
|max_duration_d|Tempo máximo de execução de uma única execução.|
|num_physical_io_reads_d|Número total de leituras físicas.|
|max_num_physical_io_reads_d|Número máximo de leituras físicas por execução.|
|log_bytes_used_d|Quantidade total de bytes de log usados.|
|max_log_bytes_used_d|Quantidade máxima de bytes de log usados por execução.|
|query_id_d|ID da consulta no Repositório de Consultas.|
|plan_id_d|ID do plano no Repositório de Consultas.|

Saiba mais sobre os [Dados de estatísticas de tempo de execução do Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Estatísticas de espera do Repositório de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|ID do locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: QueryStoreWaitStatistics|
|OperationName|Nome da operação. Sempre: QueryStoreWaitStatisticsEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados.|
|ResourceId|URI de recurso.|
|wait_category_s|Categoria da espera.|
|is_parameterizable_s|É a consulta parametrizável.|
|statement_type_s|Tipo da instrução.|
|statement_key_hash_s|Hash de chave de instrução.|
|exec_type_d|Tipo de execução.|
|total_query_wait_time_ms_d|Tempo total de espera da consulta na categoria de espera específica.|
|max_query_wait_time_ms_d|Tempo máximo de espera da consulta em execução individual na categoria de espera específica.|
|query_param_type_d|0|
|query_hash_s|Hash de consulta no Repositório de consultas.|
|query_plan_hash_s|Hash de plano de consulta no Repositório de Consultas.|
|statement_sql_handle_s|Identificador de instrução no Repositório de Consultas.|
|interval_start_time_d|Inicie o datetimeoffset do intervalo em número de tiques de 1900-1-1.|
|interval_end_time_d|Encerre o datetimeoffset do intervalo em número de tiques de 1900-1-1.|
|count_executions_d|Contagem de execuções da consulta.|
|query_id_d|ID da consulta no Repositório de Consultas.|
|plan_id_d|ID do plano no Repositório de Consultas.|

Saiba mais sobre os [Dados de estatísticas de espera no Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Conjunto de dados de erros

|Propriedade|Descrição|
|---|---|
|TenantId|ID do locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: Erros|
|OperationName|Nome da operação. Sempre: ErrorEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados.|
|ResourceId|URI de recurso.|
|Mensagem|Mensagem de erro em texto sem formatação.|
|user_defined_b|É o bit de erro definido pelo usuário.|
|error_number_d|Código do erro.|
|Severity|Gravidade do erro.|
|state_d|Estado do erro.|
|query_hash_s|Hash de consulta da consulta com falha, se disponível.|
|query_plan_hash_s|Hash do plano de consulta da consulta com falha, se disponível.|

Saiba mais sobre as [Mensagens de erro do SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Conjunto de dados das estatísticas de espera do banco de dados

|Propriedade|Descrição|
|---|---|
|TenantId|ID do locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: DatabaseWaitStatistics|
|OperationName|Nome da operação. Sempre: DatabaseWaitStatisticsEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados.|
|ResourceId|URI de recurso.|
|wait_type_s|Nome do tipo de espera.|
|start_utc_date_t [UTC]|Hora de início do período de medida.|
|end_utc_date_t [UTC]|Hora de início do período de medida.|
|delta_max_wait_time_ms_d|Tempo máximo de espera por execução|
|delta_signal_wait_time_ms_d|Tempo total de espera do sinal.|
|delta_wait_time_ms_d|Tempo total de espera no período.|
|delta_waiting_tasks_count_d|Número de tarefas em espera.|

Saiba mais sobre as [estatísticas de espera no banco de dados](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Conjunto de dados de tempos limite

|Propriedade|Descrição|
|---|---|
|TenantId|ID do locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: tempos limite|
|OperationName|Nome da operação. Sempre: TimeoutEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados.|
|ResourceId|URI de recurso.|
|error_state_d|Código do estado de erro.|
|query_hash_s|Hash de consulta, se disponível.|
|query_plan_hash_s|Hash do plano de consulta, se disponível.|

### <a name="blockings-dataset"></a>Conjunto de dados de boqueios

|Propriedade|Descrição|
|---|---|
|TenantId|ID do locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: blocos|
|OperationName|Nome da operação. Sempre: BlockEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados.|
|ResourceId|URI de recurso.|
|lock_mode_s|Modo de bloqueio usado pela consulta.|
|resource_owner_type_s|Proprietário do bloqueio.|
|blocked_process_filtered_s|XML de relatório de processo bloqueado.|
|duration_d|Duração do bloqueio em microssegundos.|

### <a name="deadlocks-dataset"></a>Conjunto de dados de deadlocks

|Propriedade|Descrição|
|---|---|
|TenantId|ID do locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC] |Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: Deadlocks|
|OperationName|Nome da operação. Sempre: Deadlocks|
|Recurso|Nome do recurso.|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados. |
|ResourceId|URI de recurso.|
|deadlock_xml_s|Relatório de deadlock XML.|

### <a name="automatic-tuning-dataset"></a>Conjunto de dados de ajuste automático

|Propriedade|Descrição|
|---|---|
|TenantId|ID do locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: AutomaticTuning|
|Recurso|Nome do recurso.|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|LogicalDatabaseName_s|Nome do banco de dados.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados.|
|ResourceId|URI de recurso.|
|RecommendationHash_s|Hash exclusivo de recomendação de ajuste automático.|
|OptionName_s|Opções de ajuste automático.|
|Schema_s|Esquema de banco de dados.|
|Table_s|Tabela afetada.|
|IndexName_s|Nome do índice.|
|IndexColumns_s|Nome da coluna.|
|IncludedColumns_s|Colunas incluídas.|
|EstimatedImpact_s|Estimado impacto recomendação de ajuste automático JSON.|
|Event_s|Tipo de evento de ajuste automático.|
|Timestamp_t|Última atualização de carimbo de hora.|

### <a name="intelligent-insights-dataset"></a>Conjunto de dados do Insights inteligentes
Saiba mais sobre o [formato de log do Insights Inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Próximas etapas

Para saber como habilitar o registro em log e entender as categorias de métrica e log com suporte dos vários serviços do Azure, leia:

 * [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Visão geral do log de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Para saber mais sobre os Hubs de Evento, leia:

* [O que é Hub de Eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Para saber mais sobre o Armazenamento, veja como [baixar métricas e logs de diagnóstico do Armazenamento](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
