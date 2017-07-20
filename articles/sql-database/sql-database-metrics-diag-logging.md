---
title: "Métricas de banco de dados SQL do Azure e o log de diagnóstico | Microsoft Docs"
description: "Saiba mais sobre como configurar recursos de banco de dados SQL do Azure para armazenar as estatísticas de execução de consulta, conectividade e uso de recursos."
services: sql-database
documentationcenter: 
author: vvasic
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: vvasic
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 71c286061e214898a7f10fa8dfd6c6d86af8db39
ms.contentlocale: pt-br
ms.lasthandoff: 06/10/2017


---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas de banco de dados SQL do Azure e o log de diagnóstico 
O Banco de Dados SQL do Azure pode emitir métrica e logs de diagnóstico para facilitar o monitoramento. Você pode configurar o Banco de Dados SQL do Azure para armazenar o uso de recursos, trabalhos, sessões e conectividade em um destes recursos do Azure:
- **Armazenamento do Azure**: para o arquivamento de grandes quantidades de telemetria por um pequeno baixo
- **Hub de Eventos do Azure**: para a integração de telemetria de Banco de Dados SQL do Azure com a sua solução de monitoramento personalizada ou pipelines ativos
- **Análise de logs do Azure**: Para solução de monitoramento fora da caixa com relatórios, alertas e recursos de mitigação 

    ![Arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Habilitar o registro em log

Métricas e log de diagnóstico não está habilitado por padrão. Você pode habilitar e gerenciar as métricas e diagnóstico de log usando um dos seguintes métodos:
- Portal do Azure
- PowerShell
- CLI do Azure
- API REST 
- Modelo do Resource Manager

Quando você habilitar o log de diagnóstico e métricas, você precisa especificar os recursos do Azure, onde os dados selecionados são coletados. Opções disponíveis:
- Log Analytics
- Hub de evento
- Armazenamento do Azure 

Você pode provisionar um novo recurso do Azure ou selecionar um recurso existente. Depois de selecionar o recurso de armazenamento, você precisa especificar quais dados coletar. As opções disponíveis incluem:

- **[métricas de 1 minuto](sql-database-metrics-diag-logging.md#1-minute-metrics)**  - contém o percentual DTU, o limite DTU, o percentual de CPU, porcentagem de leitura de dados físicos, porcentagem de gravação de log, êxito/falha/bloqueados por conexões de firewall, porcentagem de sessões, porcentagem de funcionários, armazenamento, porcentagem de armazenamento, porcentagem de armazenamento XTP

Se você especificar uma conta de AzureStorage ou Hub de eventos, você pode especificar uma política de retenção para especificar que os dados mais antigos do que um período de tempo selecionado é excluído. Se você especificar a análise de Log, a política de retenção depende do tipo de preço selecionado. Saiba mais sobre [Preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/). 

Recomendamos que você leia os artigos [Visão geral de métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Visão geral dos Logs de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para entender não apenas como habilitar o registro em log, mas também as métricas e categorias de log com suporte dos vários serviços do Azure.

### <a name="azure-portal"></a>Portal do Azure

Para habilitar as métricas e coleta de logs de diagnóstico no portal do Azure, navegue até o banco de dados SQL Azure ou página de pool elástico e, em seguida, clique em **Configurações de diagnóstico**.

   ![habilitar no portal do Azure](./media/sql-database-metrics-diag-logging/enable-portal.png)

### <a name="powershell"></a>PowerShell

Para habilitar as métricas e o log de diagnóstico usando o PowerShell, use os seguintes comandos:

- Para habilitar o armazenamento dos Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da Conta de Armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

- Para habilitar o streaming dos Logs de Diagnóstico para um Hub de Eventos, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para habilitar o envio dos Logs de Diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- É possível obter a ID de recurso do seu espaço de trabalho do Log Analytics usando o seguinte comando:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="cli"></a>CLI

Para habilitar as métricas e o log de diagnóstico usando o CLI do Azure, use os seguintes comandos:

- Para habilitar o armazenamento dos Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A ID da Conta de Armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

- Para habilitar o streaming dos Logs de Diagnóstico para um Hub de Eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar o envio dos Logs de Diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba sobre como [alterar as Configurações de Diagnóstico usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Leia sobre como [habilitar as Configurações de Diagnóstico na criação do recurso usando o modelo do Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Fluxo em Log Analytics 
Métricas de banco de dados SQL do Azure e logs de diagnóstico podem ser transmitidos em Log Analytics usando a opção "Enviar para análise de Log" incorporada no portal ou, habilitando o Log Analytics em uma configuração de diagnóstico por meio de cmdlets do PowerShell do Azure, CLI do Azure ou API REST do Azure Monitor.

### <a name="installation-overview"></a>Visão geral da instalação

Monitorar fleet de banco de dados SQL do Azure é simples com Log Analytics. Três etapas são necessárias:

1.  Criar recursos de Log Analytics
2.  Configurar bancos de dados para gravar logs de diagnóstico e métricas para o Log Analytics criado
3.  Instalar a solução de **Análise de SQL do Azure** na galeria do Log Analytics

### <a name="create-log-analytics-resource"></a>Criar recursos de Log Analytics

1. Clique em **Novo** no menu da esquerda.
2. Clique em **Monitoramento + Gerenciamento**
3. Clique em **Log Analytics**
4. Preencha o formulário de Log Analytics com as informações adicionais necessárias: nome do espaço de trabalho, assinatura, grupo de recursos, local e tipo de preço.

   ![log analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostic-logs"></a>Configurar bancos de dados para gravar logs de diagnóstico e métricas

A maneira mais fácil de configurar onde os bancos de dados registram suas métricas é por meio do portal do Azure. No portal do Azure, navegue até o recurso de banco de dados SQL do Azure e clique em **Configurações de diagnóstico**. 

### <a name="install-the-azure-sql-analytics-solution-from-gallery"></a>Instalar a solução de Análise de SQL do Azure na galeria  

1. Depois que o recurso de Log Analytics é criado e seus dados estão fluindo nele, instale a solução de análise de SQL do Azure. Isso pode ser feito por meio da **Galeria de soluções** que você pode encontrar na home page do OMS e no menu lateral. Na galeria, localize e clique em solução de **Análise de SQL do Azure** e clique em **Adicionar**.

   ![solução de monitoramento](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. Na home page do seu OMS, um novo bloco chamado **Análise de SQL do Azure** é exibido. Selecionar este bloco abre o painel de Análise de SQL do Azure.

### <a name="using-azure-sql-analytics-solution"></a>Usando a solução de Análise de SQL do Azure

Análise de SQL do Azure é um painel hierárquico que permite navegar pela hierarquia de recursos de banco de dados SQL do Azure. Esse recurso permite que você faça o monitoramento de alto nível, mas ele também permite definir o escopo de seu monitoramento para o conjunto correto de recursos.
O painel contém a lista de recursos diferentes sob o recurso selecionado. Por exemplo, para uma assinatura selecionada, você pode ver todos os servidores, pools elásticos e bancos de dados que pertencem à assinatura selecionada. Além disso, para Pools Elásticos e bancos de dados, você pode ver as métricas de uso de recursos daquele recurso. Isso inclui gráficos para DTU, CPU, IO, LOG, sessões, operadores, conexões e armazenamento em GB.

## <a name="stream-into-azure-event-hub"></a>Fluxo no Hub de eventos do Azure

Métricas de banco de dados SQL do Azure e logs de diagnóstico podem ser transmitidos em Hub de eventos usando a opção "Enviar para um hub de eventos" incorporada no portal ou, habilitando a Regra de Barramento de Serviço em uma configuração de diagnóstico por meio de Cmdlets do PowerShell do Azure, CLI do Azure ou API REST do Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>O que fazer com as métricas e os logs de diagnóstico no Hub de eventos?
Depois que os dados selecionados são transmitidos para o Hub de eventos, você está mais próximo de habilitar cenários de monitoramento avançado. Os Hub de Eventos agem como a "porta de entrada” para um pipeline de eventos e depois que os dados são coletados em um Hub de Eventos, eles podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Hub de Eventos separa a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam acessar os eventos em seu próprio cronograma. Para obter mais informações sobre o Hub de eventos, consulte:

- [O que são Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md)?
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Veja algumas maneiras de usar o recurso de streaming:

-   Exibir a integridade do serviço transmitindo dados de "afunilamento" para o Power BI - Usando os Hubs de Eventos, Stream Analytics e o Power BI, é fácil transformar suas métricas e dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Para uma visão geral de como configurar um Hubs de Eventos, processar dados com o Stream Analytics e usar o Power BI como uma saída, consulte [Stream Analytics e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).
-   Logs de fluxo para fluxos de log e telemetria de terceiros – Usando a transmissão de Hubs de eventos, você pode obter suas métricas e logs de diagnóstico para soluções de log analytics e monitoramento de terceiros diferentes. 
-   Criar uma plataforma de registro em log e telemetria personalizada – Se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a flexibilidade de ingestão de logs de diagnóstico. Consulte [o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Fluxo no Armazenamento do Azure

Métricas de banco de dados SQL do Azure e logs de diagnóstico podem ser armazenados no Armazenamento do Azure usando a opção "Arquivar para uma conta de armazenamento" incorporada no portal do Azure, ou habilitando o Armazenamento do Azure em uma configuração de diagnóstico por meio de Cmdlets do PowerShell do Azure, CLI do Azure ou API REST do Azure Monitor.

### <a name="schema-of-metrics-and-diagnostic-logs-in-the-storage-account"></a>Esquema de métricas e logs de diagnóstico na conta de armazenamento

Depois que você configurar as métricas e coleta de logs de diagnóstico, um contêiner de armazenamento é criado na conta de armazenamento que você selecionou quando as primeiras linhas de dados estão disponíveis. A estrutura desses blobs é:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Ou, simplesmente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, um nome de blob para métricas de 1 minuto pode ser:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

No caso de você desejar registrar os dados do Pool Elástico, o nome do blob é um pouco diferente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-azure-storage"></a>Baixar logs e métricas do Armazenamento do Azure

Consulte [Baixar métricas e logs de diagnósticos do Armazenamento do Azure](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)

## <a name="1-minute-metrics"></a>métricas de 1 minuto

| |  |
|---|---|
|**Recurso**|**Métricas**|
|Banco de dados|porcentagem de DTU, DTU usado, o limite DTU, o porcentagem de CPU, porcentagem de leitura de dados físicos, porcentagem de gravação de log, êxito/falha/bloqueados por conexões de firewall, porcentagem de sessões, porcentagem de funcionários, armazenamento, porcentagem de armazenamento, porcentagem de armazenamento XTP, deadlocks |
|Pool elástico|porcentagem de eDTU, eDTU usado, o limite eDTU, porcentagem de CPU, porcentagem de leitura de dados físicos, porcentagem de gravação de log, porcentagem de sessões, porcentagem de funcionários, armazenamento, porcentagem de armazenamento, limite de armazenamento, porcentagem de armazenamento XTP |
|||

## <a name="next-steps"></a>Próximas etapas

- Leia os artigos [Visão geral de métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Visão geral dos Logs de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para entender não apenas como habilitar o registro em log, mas também as métricas e categorias de log com suporte dos vários serviços do Azure.
- Leia estes artigos para saber mais sobre os hubs de eventos:
   - [O que são Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md)?
   - [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Consulte [Baixar métricas e logs de diagnósticos do Armazenamento do Azure](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)

