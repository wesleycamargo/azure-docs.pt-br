---
title: "Visão geral dos Logs de Diagnóstico do Azure | Microsoft Docs"
description: "Saiba quais são os Logs de Diagnóstico do Azure e como você pode usá-los para compreender os eventos que ocorrem dentro de um recurso do Azure."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: johnkem; magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: c3e766b2e4c7bd372dc9c7b9d675a76c2d14a4cc
ms.contentlocale: pt-br
ms.lasthandoff: 05/20/2017


---
# <a name="collect-and-consume-diagnostic-data-from-your-azure-resources"></a>Coletar e consumir dados de diagnóstico dos recursos do Azure

## <a name="what-are-azure-diagnostic-logs"></a>O que são os Logs de Diagnóstico do Azure
**Os Logs de Diagnóstico do Azure** são logs emitidos por um recurso que fornece dados avançados e frequentes sobre a operação do recurso. O conteúdo desses logs varia de acordo com o tipo de recurso. Por exemplo, os logs de eventos do sistema Windows são uma categoria de Log de Diagnóstico para VMs, e logs de blobs, tabelas e filas são categorias de Logs de Diagnóstico para contas de armazenamento.

Os logs de diagnóstico diferem do [Log de Atividades (anteriormente conhecido como Log de Auditoria ou Log Operacional)](monitoring-overview-activity-logs.md). O Log de Atividades fornece informações sobre as operações executadas em recursos em sua assinatura. Os Logs de Diagnóstico fornecem informações em operações que o recurso realizou por conta própria.

Nem todos os recursos suportam o novo tipo de Logs de Diagnóstico descrito aqui. Este artigo contém uma seção que lista os tipos de recursos que dão suporte a novos Logs de Diagnóstico.

![Logs de Diagnóstico X outros tipos de logs ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Figura 1: Logs de Diagnóstico X outros tipos de logs

## <a name="what-you-can-do-with-diagnostic-logs"></a>O que você pode fazer com os Logs de Diagnóstico
Aqui estão algumas coisas que você pode fazer com os Logs de Diagnóstico:

![Posicionamento lógico dos Logs de Diagnóstico](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Salve-os em uma [**Conta de Armazenamento**](monitoring-archive-diagnostic-logs.md) para auditoria ou inspeção manual. Você pode especificar o tempo (em dias) de retenção usando as **Configurações de Diagnóstico**.
* [Transmita-os para os **Hubs de Eventos**](monitoring-stream-diagnostic-logs-to-event-hubs.md) para o consumo por um serviço de terceiros ou uma solução de análises personalizadas, como o PowerBI.
* Analise-os com o [Log Analytics do OMS](../log-analytics/log-analytics-azure-storage.md)

Você pode usar uma conta de armazenamento ou um namespace de hub de eventos que não esteja na mesma assinatura para emitir logs. O usuário que define a configuração deve ter o devido acesso RBAC para ambas as assinaturas.

## <a name="diagnostic-settings"></a>Configurações de Diagnóstico
Os Logs de Diagnóstico para os recursos de Não Computação são configurados usando as Configurações de Diagnóstico. **Configurações de Diagnóstico** para um controle de recursos:

* Para onde os Logs de Diagnóstico são enviados (Conta de Armazenamento, Hubs de Eventos e/ou Log Analytics do OMS).
* Quais Categorias de Log são enviadas.
* Quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento
    - Uma retenção de zero dias significa que os registros serão mantidos indefinidamente. O valor pode ser qualquer quantidade de dias, entre 1 e 2147483647.
    - Se as políticas de retenção são definidas, mas o armazenamento dos logs em uma Conta de Armazenamento está desabilitado (por exemplo, se apenas as opções Hubs de Eventos ou OMS estão selecionadas), as políticas de retenção não têm nenhum efeito.
    - As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos.

Essas configurações são facilmente definidas via folha Diagnóstico para um recurso no Portal do Azure, via Azure PowerShell e comandos da CLI ou via [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Os Logs de Diagnóstico e as métricas para os recursos de Computação (por exemplo, VMs ou Service Fabric) usam [um mecanismo separado para a configuração e a seleção de saídas](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Como habilitar a coleção de Logs de Diagnóstico
A coleção de Logs de Diagnóstico pode ser habilitada como parte da criação de um recurso ou depois de um recurso ser criado via folha do recurso no Portal. Você também pode habilitar os Logs de Diagnóstico a qualquer momento usando os comandos do Azure PowerShell ou da CLI, ou usando a API REST do Azure Monitor.

> [!TIP]
> Essas instruções não podem ser aplicadas diretamente em cada recurso. Confira os links do esquema na parte inferior desta página para entender as etapas especiais que podem ser aplicadas em certos tipos de recursos.
>
>

[Este artigo mostra como você pode usar um modelo de recursos para habilitar as Configurações de Diagnóstico ao criar um recurso](monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Habilite os Logs de Diagnóstico no portal
Você pode habilitar os Logs de Diagnóstico no portal do Azure ao criar tipos de recursos de computação habilitando a extensão de Diagnóstico do Azure para Windows ou Linux:

1. Vá para **Novo** e escolha o recurso em que você está interessado.
2. Depois de definir as configurações básicas e selecionar um tamanho, na folha **Configurações**, em **Monitoramento**, selecione **Habilitado** e escolha uma conta de armazenamento na qual você gostaria de armazenar os Logs de Diagnóstico. Você será cobrado pelas taxas de dados normais para o armazenamento e as transações quando enviar diagnósticos para uma conta de armazenamento.

   ![Habilitar Logs de Diagnóstico durante a criação de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3. Clique em **OK** e crie o recurso.

Para recursos que não são de computação, você pode habilitar os Logs de Diagnóstico no Portal do Azure depois de um recurso ser criado fazendo o seguinte:

1. Vá para a folha do recurso e abra folha **Diagnóstico** .
2. Clique em **Ativar** e selecione uma Conta de Armazenamento e/ou Hub de Eventos.

   ![Habilitar Logs de Diagnóstico depois da criação de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. Em **Logs**, selecione quais **Categorias de Log** você gostaria de coletar ou transmitir.
4. Clique em **Salvar**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Habilitar os Logs de Diagnóstico pelo PowerShell
Para habilitar os Logs de Diagnóstico via Cmdlets do Azure PowerShell, use os comandos a seguir.

Para habilitar o armazenamento dos Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

A ID da Conta de Armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

Para habilitar o streaming dos Logs de Diagnóstico para um Hub de Eventos, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para habilitar o envio dos Logs de Diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

É possível obter a ID de recurso do seu espaço de trabalho do Log Analytics usando o seguinte comando:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="enable-diagnostic-logs-via-cli"></a>Habilitar os Logs de Diagnóstico pela CLI
Para habilitar os Logs de Diagnóstico via CLI do Azure, use os comandos a seguir:

Para habilitar o armazenamento dos Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

A ID da Conta de Armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

Para habilitar o streaming dos Logs de Diagnóstico para um Hub de Eventos, use este comando:

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para habilitar o envio dos Logs de Diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Habilitar os Logs de Diagnóstico usando a API REST
Para alterar as Configurações de Diagnóstico usando a API REST do Azure Monitor, confira [este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Gerenciar Configurações de Diagnóstico no portal
Verifique se todos os seus recursos estão definidos com as configurações de diagnóstico. Navegue até a folha **Monitoramento** no portal e abra a folha **Logs de Diagnóstico**.

![Folha Logs de Diagnóstico no portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Você talvez tenha que clicar em "Mais serviços" para localizar a folha Monitoramento.

Nessa folha, você pode exibir e filtrar todos os recursos que dão suporte a Logs de Diagnóstico para ver se eles têm o diagnóstico habilitado. Você também pode verificar para qual conta de armazenamento, hub de eventos e/ou espaço de trabalho do Log Analytics esses logs estão fluindo.

![Resultados da folha Logs de Diagnóstico no portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Clicar em um recurso mostrará todos os logs que foram colocados na conta de armazenamento e oferecem a opção para desativar ou modificar as configurações do diagnóstico. Clique no ícone de download para baixar os logs por um período de tempo específico.

![Folha Logs de Diagnóstico com um recurso](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> Os logs de diagnóstico aparecem apenas nesta exibição e ficam disponíveis para download se você definiu as configurações de diagnóstico para salvá-los em uma conta de armazenamento.
>
>

Clique no link de **Configurações de diagnóstico** para mostrar a folha de configurações de diagnóstico, onde você pode habilitar, desabilitar ou modificar as configurações de diagnóstico para o recurso selecionado.

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Serviços e esquema com suporte para os Logs de Diagnóstico
O esquema para os Logs de Diagnóstico varia dependendo do recurso e da categoria do log.   

| O Barramento de | Esquema e Documentos |
| --- | --- |
| Gerenciamento da API | Esquema não disponível. |
| Gateways do Aplicativo |[Log de diagnóstico do Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Automação do Azure |[Análise de log para automação do Azure](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Lote do Azure |[Logs de diagnóstico do Lote do Azure](../batch/batch-diagnostics.md) |
| Customer Insights | Esquema não disponível. |
| Rede de Distribuição de Conteúdo | Esquema não disponível. |
| Análises Data Lake |[Acessando os logs de diagnóstico do Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Repositório Data Lake |[Acessando os logs de diagnóstico do Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hubs de Eventos |[Logs de diagnóstico dos Hubs de Eventos do Azure](../event-hubs/event-hubs-diagnostic-logs.md) |
| Cofre da Chave |[Logs do Cofre da Chave do Azure](../key-vault/key-vault-logging.md) |
| Balanceador de carga |[Log Analytics para o Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Aplicativos Lógicos |[Esquema de controle personalizado dos Aplicativos Lógicos B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de segurança de rede |[Análise de logs para NSGs (grupos de segurança de rede)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Serviços de Recuperação | Esquema não disponível.|
| Pesquisar |[Habilitação e uso da análise de tráfego de pesquisa](../search/search-traffic-analytics.md) |
| Gerenciamento do Servidor | Esquema não disponível. |
| Barramento de Serviço |[Logs de diagnóstico do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Análise de fluxo |[Logs de diagnóstico do trabalho](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |

## <a name="supported-log-categories-per-resource-type"></a>Categorias de log com suporte por tipo de recurso
|Tipo de recurso|Categoria|Nome de exibição da categoria|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|Logs relacionados ao Gateway ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Logs de trabalho|
|Microsoft.Automation/automationAccounts|JobStreams|Transmissões de trabalho|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Status do nó DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Logs de serviço|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtém as métricas do ponto de extremidade, como largura de banda, saída etc.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataLakeAnalytics/accounts|Audit|Logs de Auditoria|
|Microsoft.DataLakeAnalytics/accounts|Solicitações|Logs de solicitação|
|Microsoft.DataLakeStore/accounts|Audit|Logs de Auditoria|
|Microsoft.DataLakeStore/accounts|Solicitações|Logs de solicitação|
|Microsoft.EventHub/namespaces|ArchiveLogs|Logs de arquivo|
|Microsoft.EventHub/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Logs de Escala Automática|
|Microsoft.KeyVault/vaults|AuditEvent|Logs de Auditoria|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico de tempo de execução do fluxo de trabalho|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Acompanhar os eventos da Conta de Integração|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Network Security Group Event|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de regras de grupo de segurança de rede|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta do Load Balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Status de integridade da investigação do Load Balancer|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log de acesso do Gateway de Aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Log de desempenho do Gateway de Aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Log de firewall do Gateway de Aplicativo|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dados de relatórios de backup do Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Trabalhos do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventos do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Itens replicados do Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Logs de operação|
|Microsoft.ServerManagement/nodes|RequestLogs|Logs de solicitação|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Criação|Criação|

## <a name="next-steps"></a>Próximas etapas

* [Transmitir Logs de Diagnóstico para os **Hubs de Eventos**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Alterar as configurações de diagnóstico usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](../log-analytics/log-analytics-azure-storage.md)

