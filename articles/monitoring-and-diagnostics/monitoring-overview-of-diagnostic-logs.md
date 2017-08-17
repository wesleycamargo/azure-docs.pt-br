---
title: "Visão geral dos Logs de Diagnóstico do Azure | Microsoft Docs"
description: "Saiba quais são os Logs de Diagnóstico do Azure e como você pode usá-los para compreender os eventos que ocorrem dentro de um recurso do Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: johnkem; magoedte
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 8961676a60d922912e383937ca38c5d2f89a348a
ms.contentlocale: pt-br
ms.lasthandoff: 08/04/2017

---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Coletar e consumir dados de log dos recursos do Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>O que são os Logs de Diagnóstico de recursos do Azure
**Os Logs de Diagnóstico no nível do recurso do Azure** são logs emitidos por um recurso que fornece dados avançados e frequentes sobre a operação do recurso. O conteúdo desses logs varia de acordo com o tipo de recurso. Por exemplo, os contadores de regra do Grupo de Segurança de Rede e as auditorias do Key Vault são duas categorias de logs de recursos.

Os logs de diagnóstico no nível do recurso são diferentes do [Log de Atividades](monitoring-overview-activity-logs.md). O Log de Atividades fornece informações sobre as operações executadas em recursos em sua assinatura usando o Gerenciador de Recursos, por exemplo, criar uma máquina virtual ou excluir um aplicativo lógico. O Log de Atividades é um log no nível da assinatura. Os logs de diagnóstico no nível do recurso fornecem informações sobre as operações executadas dentro do próprio recurso, por exemplo, obtenção de um segredo de um Key Vault.

Os logs de diagnóstico no nível do recurso também diferem dos logs de diagnóstico no nível do sistema operacional convidado. Os logs de diagnóstico do sistema operacional convidado são aqueles coletados por um agente em execução dentro de uma máquina virtual ou outro tipo de recurso com suporte. Os logs de diagnóstico no nível do recurso não exigem um agente e capturam dados específicos ao recurso da própria plataforma do Azure, enquanto os logs de diagnóstico no nível do sistema operacional convidado capturam dados do sistema operacional e de aplicativos em execução em uma máquina virtual.

Nem todos os recursos oferecem suporte ao novo tipo de logs de diagnóstico descritos aqui. Este artigo contém uma seção que lista os tipos de recursos que dão suporte aos novos logs de diagnóstico no nível do recurso.

![Logs de Diagnóstico do recurso X outros tipos de logs ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Figura 1: Logs de Diagnóstico do recurso X outros tipos de logs

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>O que você pode fazer com os Logs de Diagnóstico no nível do recurso
Aqui estão algumas coisas que você pode fazer com os logs de diagnóstico de recurso:

![Posicionamento lógico dos Logs de Diagnóstico de recurso](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Salve-os em uma [**Conta de Armazenamento**](monitoring-archive-diagnostic-logs.md) para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as **Configurações de Diagnóstico do Recurso**.
* [Transmita-os para os **Hubs de Eventos**](monitoring-stream-diagnostic-logs-to-event-hubs.md) para o consumo por um serviço de terceiros ou uma solução de análises personalizadas, como o PowerBI.
* Analise-os com o [Log Analytics do OMS](../log-analytics/log-analytics-azure-storage.md)

Você pode usar uma conta de armazenamento ou um namespace de Hubs de Evento que não esteja na mesma assinatura que os logs emissores. O usuário que define a configuração deve ter o devido acesso RBAC para ambas as assinaturas.

## <a name="resource-diagnostic-settings"></a>Configurações do Diagnóstico do recurso
Os logs de diagnóstico do recurso para os recursos que não são de computação são configurados usando as configurações de diagnóstico do recurso. **Configurações de Diagnóstico do Recurso** para um controle de recursos:

* Para onde os logs de diagnóstico do recurso são enviados (Conta de Armazenamento, Hubs de Eventos e/ou Log Analytics do OMS).
* Quais categorias de log são enviadas.
* Quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento
    - Uma retenção de zero dias significa que os registros serão mantidos indefinidamente. O valor pode ser qualquer quantidade de dias, entre 1 e 2147483647.
    - Se as políticas de retenção são definidas, mas o armazenamento dos logs em uma Conta de Armazenamento está desabilitado (por exemplo, se apenas as opções Hubs de Eventos ou OMS estão selecionadas), as políticas de retenção não têm nenhum efeito.
    - As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos.

Essas configurações são facilmente definidas via folha diagnóstico para um recurso no Portal do Azure, via Azure PowerShell e comandos da CLI ou via [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Os Logs de Diagnóstico e as métricas para os recursos de Computação (por exemplo, VMs ou Service Fabric) usam [um mecanismo separado para a configuração e a seleção de saídas](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Como habilitar a coleção de logs de diagnóstico do recurso
A coleção de logs de diagnóstico do recurso pode ser habilitada [como parte da criação de um recurso em um modelo do Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) ou depois de um recurso ser criado via folha do recurso no Portal. Você também pode habilitar a coleção a qualquer momento usando os comandos do Azure PowerShell ou da CLI, ou usando a API REST do Azure Monitor.

> [!TIP]
> Essas instruções não podem ser aplicadas diretamente em cada recurso. Confira os links do esquema na parte inferior desta página para entender as etapas especiais que podem ser aplicadas em certos tipos de recursos.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Como habilitar a coleção de logs de diagnóstico de recurso no Portal
Habilite a coleção de logs de diagnóstico de recurso no Portal do Azure, após a criação de um recurso por meio destas etapas:

1. Vá para a folha do recurso e abra folha **Diagnóstico** .
2. Clique em **Ativar** e selecione uma Conta de Armazenamento e/ou hub de eventos.

   ![Habilitar Logs de Diagnóstico depois da criação de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. Em **Logs**, selecione quais **Categorias de Log** você gostaria de coletar ou transmitir.
4. Clique em **Salvar**.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Habilitar a coleção de logs de diagnóstico de recurso via PowerShell
Para habilitar a coleção de logs de diagnóstico de recurso via Azure PowerShell, use os comandos a seguir:

Para habilitar o armazenamento dos logs de diagnóstico em uma conta de armazenamento, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

A ID da conta de armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

Para habilitar o streaming dos logs de diagnóstico para um hub de eventos, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

A ID da regra do barramento de serviço é uma cadeia de caracteres com este formato: `{Service Bus resource ID}/authorizationrules/{key name}`.

Para habilitar o envio dos logs de diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

É possível obter a ID de recurso do seu espaço de trabalho do Log Analytics usando o seguinte comando:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>Habilitar a coleção de logs de diagnóstico do recurso via CLI
Para habilitar a coleção de logs de diagnóstico de recurso via CLI do Azure, use os comandos a seguir:

Para habilitar o armazenamento dos logs de diagnóstico em uma conta de armazenamento, use este comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

A ID da conta de armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

Para habilitar o streaming dos logs de diagnóstico para um hub de eventos, use este comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

A ID da regra do barramento de serviço é uma cadeia de caracteres com este formato: `{Service Bus resource ID}/authorizationrules/{key name}`.

Para habilitar o envio dos logs de diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Habilitar a coleção de logs de diagnóstico do recurso via API REST
Para alterar as Configurações de Diagnóstico usando a API REST do Azure Monitor, confira [este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Gerenciar configurações de diagnóstico de recurso no Portal
Verifique se todos os seus recursos estão definidos com as configurações de diagnóstico. Navegue até a folha **Monitoramento** no portal e abra a folha **Logs de Diagnóstico**.

![Folha Logs de Diagnóstico no portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Você talvez tenha que clicar em "Mais serviços" para localizar a folha Monitoramento.

Nessa folha, você pode exibir e filtrar todos os recursos que dão suporte a logs de diagnóstico para ver se eles têm o diagnóstico habilitado. Você também pode verificar para qual conta de armazenamento, hub de eventos e/ou espaço de trabalho do Log Analytics esses logs estão fluindo.

![Resultados da folha Logs de Diagnóstico no portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Clicar em um recurso mostrará todos os logs que foram colocados na conta de armazenamento e oferecem a opção para desativar ou modificar as configurações do diagnóstico. Clique no ícone de download para baixar os logs por um período de tempo específico.

![Folha Logs de Diagnóstico com um recurso](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> Os logs de diagnóstico aparecem apenas nesta exibição e ficam disponíveis para download se você definiu as configurações de diagnóstico para salvá-los em uma conta de armazenamento.
>
>

Clique no link de **Configurações de diagnóstico** para mostrar a folha de configurações de diagnóstico, onde você pode habilitar, desabilitar ou modificar as configurações de diagnóstico para o recurso selecionado.

## <a name="supported-services-and-schema-for-resource-diagnostic-logs"></a>Serviços e esquema com suporte para os logs de diagnóstico de recurso
O esquema para os logs de diagnóstico de recurso varia dependendo do recurso e da categoria do log.   

| O Barramento de | Esquema e Documentos |
| --- | --- |
| Gerenciamento da API | Esquema não disponível. |
| Gateways do Aplicativo |[Log de diagnóstico do Gateway de Aplicativo](../application-gateway/application-gateway-diagnostics.md) |
| Automação do Azure |[Análise de log para automação do Azure](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Lote do Azure |[Logs de diagnóstico do Lote do Azure](../batch/batch-diagnostics.md) |
| Customer Insights | Esquema não disponível. |
| Rede de Distribuição de Conteúdo | Esquema não disponível. |
| CosmosDB | Esquema não disponível. |
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
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
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
|Microsoft.ServiceBus/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Criação|Criação|

## <a name="next-steps"></a>Próximas etapas

* [Transmitir logs de diagnóstico de recurso os **Hubs de Eventos**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Alterar as configurações de diagnóstico do recurso usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](../log-analytics/log-analytics-azure-storage.md)

