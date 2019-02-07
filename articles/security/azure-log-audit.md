---
title: Log e auditoria do Azure | Microsoft Docs
description: Saiba mais sobre como você pode usar os dados de log para obter informações detalhadas sobre seu aplicativo.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: 574c295c3b44d0c6146c852777f2bef3ad13ecdd
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697910"
---
# <a name="azure-logging-and-auditing"></a>Log e auditoria do Azure

O Azure fornece uma ampla gama de opções de registro de log e auditoria de segurança configuráveis para ajudá-lo a identificar lacunas em seus mecanismos e políticas de segurança. Este artigo discute a geração, a coleta e a análise de logs de segurança de serviços hospedados no Azure.

> [!Note]
> Determinadas recomendações neste artigo podem resultar em maior uso de recursos de computação, rede ou dados e aumentar os custos de licença ou assinatura.

## <a name="types-of-logs-in-azure"></a>Tipos de logs no Azure

Os aplicativos em nuvem são complexos com muitas partes móveis. Os logs fornecem dados para ajudar a manter seus aplicativos em funcionamento. Os logs ajudam você a solucionar problemas anteriores ou evitar potenciais problemas. E eles podem ajudá-lo a melhorar o desempenho ou a capacidade de manutenção do aplicativo ou automatizar ações que, de outra forma, exigiriam intervenção manual.

Os logs do Azure são categorizados nos seguintes tipos:
* **Logs de controle/gerenciamento** fornecem informações sobre as operações CREATE, UPDATE e DELETE do Azure Resource Manager. Para obter mais informações, confira [Logs de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Logs de plano de dados** fornecem informações sobre eventos gerados como parte de uso de recursos do Azure. Exemplos desse tipo de log são os logs do aplicativo, de segurança e do sistema de eventos do Windows em uma VM (máquina virtual) e os [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) que são configurados por meio do Azure Monitor.

* Os **eventos processados** fornecem informações sobre os eventos/alertas analisados que foram processados em seu nome. Exemplos desse tipo são os [Alertas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts), nos quais a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) processou e analisou sua assinatura e fornece alertas de segurança concisos.

A tabela a seguir lista os tipos mais importantes de logs disponíveis no Azure:

| Categoria do log | Tipo de log | Uso | Integração |
| ------------ | -------- | ------ | ----------- |
|[Logs de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Eventos de plano de controle nos recursos do Azure Resource Manager|   Fornecem informações sobre as operações executadas em recursos de sua assinatura.|    API REST, [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Logs de diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Dados frequentes sobre a operação de recursos do Azure Resource Manager na assinatura|    Fornecem informações sobre as operações que o recurso executou por conta própria.| Azure Monitor, [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Relatórios do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Logs e relatórios | Relata atividades de conexão do usuário e informações de atividades do sistema sobre gerenciamento de usuários e grupos.|[API do Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Máquinas virtuais e serviços de nuvem](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Serviço de log de eventos do Windows e syslog do Linux|  Capturam dados do sistema e dados de logs nas máquinas virtuais e transferem os dados para uma conta de armazenamento de sua escolha.|   Windows (usando o armazenamento [WAD](https://docs.microsoft.com/azure/azure-diagnostics) [Diagnóstico do Azure para Windows]) e Linux no Azure Monitor|
|[Análise do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Log de armazenamento, fornece dados de métrica de uma conta de armazenamento|Fornece informações das solicitações de rastreamento, analisa tendências de uso e diagnostica problemas com a conta de armazenamento.|   API REST ou [biblioteca de cliente](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Logs de fluxo do NSG (Grupo de Segurança de Rede)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Formato JSON, mostra os fluxos de entrada e saída por regra|Exibe informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança de Rede.|[Observador de Rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Logs, exceções e diagnóstico personalizado|   Fornece um serviço APM (monitoramento de desempenho de aplicativos) para desenvolvedores da Web em várias plataformas.| API REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Dados do processo/alertas de segurança|    Alertas da Central de Segurança do Azure, alertas do Azure Log Analytics|   Fornece informações e alertas de segurança.|  APIs REST, JSON|

### <a name="activity-logs"></a>Logs de atividade

Os [logs de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos em sua assinatura. Os logs de atividades eram conhecidos como “logs de auditoria” ou “logs operacionais”, pois eles relatam [eventos de plano de controle](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) de suas assinaturas. 

Os logs de atividades ajudam você a determinar o "o quê, quem e quando" para operações de gravação (ou seja, PUT, POST ou DELETE). Os logs de atividades também ajudam a entender o status da operação e outras propriedades relevantes. Os logs de atividade não incluem operações de leitura (GET).

Neste artigo, PUT, POST e DELETE referem-se a todas as operações de gravação que o log de atividades contém nos recursos. Por exemplo, é possível usar os logs de atividades para encontrar um erro ao solucionar problemas ou para monitorar como um usuário de sua organização modificou um recurso.

![Diagrama do log de atividades](./media/azure-log-audit/azure-log-audit-fig1.png)

Você pode recuperar os eventos do log de atividades usando o portal do Azure, a [CLI do Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli), os cmdlets do PowerShell e a [API REST do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Os logs de atividades têm um período de retenção de dados de 90 dias.

Cenários de integração para um evento de log de atividades:

* [Criar um alerta de email ou webhook disparado por um evento de log de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Transmiti-lo para um hub de eventos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o Power BI.

* Analisá-lo no PowerBI usando o [Pacote de conteúdo do PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Salvá-lo em uma conta de armazenamento para inspeção manual ou arquivamento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Você pode especificar o tempo de retenção (em dias) usando os perfis de log.

* Consultar e exibi-lo no portal do Azure.

* Consultar por meio de cmdlet do PowerShell, da CLI do Azure ou da API REST.

* Exportar o log de atividades com perfis de log para o [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

É possível usar uma conta de armazenamento ou um [namespace de hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) que não está na mesma assinatura que está emitindo o log. Qualquer que seja a pessoa que define a configuração deve ter o devido acesso [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) para ambas as assinaturas.

### <a name="azure-diagnostics-logs"></a>Logs de diagnóstico do Azure

Os logs de diagnóstico do Azure são emitidos por um recurso que fornece dados avançados e frequentes sobre a operação do recurso. O conteúdo desses logs varia de acordo com o tipo de recurso. Por exemplo, os [logs do sistema de eventos do Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) são uma categoria de logs de diagnóstico para VMs e [logs de blobs, de tabelas e de filas são](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) categorias de logs de diagnóstico para contas de armazenamento. Os logs de diagnóstico são diferentes dos logs de atividades, que fornecem insights sobre as operações que foram executadas em recursos em sua assinatura.

![Diagramas de logs de diagnóstico do Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Os logs de diagnóstico do Azure oferecem várias opções de configuração, como o portal do Azure, o PowerShell, a CLI do Azure e a API REST.

**Cenários de integração**

* Salve-os em uma [conta de armazenamento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) para auditoria ou inspeção manual. Você pode especificar o tempo (em dias) de retenção usando as configurações de diagnóstico.

* [Transmita-os para os Hubs de Eventos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) para ingestão por um serviço de terceiros ou uma solução de análises personalizadas, como o [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analise-os com o [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Serviços compatíveis, esquema dos logs de diagnóstico e categorias de logs compatíveis por tipo de recurso**


| Serviço | Esquema e documentação | Tipo de recurso | Categoria |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Log Analytics para Load Balancer (versão prévia)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Grupos de segurança de rede|[Log Analytics para Grupos de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Gateway de Aplicativo do Azure|[Log de diagnóstico do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Cofre da Chave do Azure|[Logs do Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Habilitação e uso da análise de tráfego de pesquisa](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Repositório Azure Data Lake|[Acessar os logs de diagnóstico do Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Audit<br>Requests|
|Análise Azure Data Lake|[Acessar os logs de diagnóstico do Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Audit<br>Requests|
|Aplicativos Lógicos do Azure|[Esquema de controle personalizado dos Aplicativos Lógicos B2B](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Lote do Azure|[Logs de diagnóstico do Lote do Azure](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Automação do Azure|[Log Analytics para Automação do Azure](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Hubs de eventos do Azure|[Logs de diagnóstico dos Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Stream Analytics do Azure|[Logs de diagnóstico de trabalho](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Execução<br>Criação|
|Barramento de Serviço do Azure|[Logs de diagnóstico dos Barramento de Serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Relatórios do Azure Active Directory

O Azure AD (Azure Active Directory) inclui relatórios de segurança, de atividades e de auditoria para o diretório de um usuário. O [relatório de auditoria do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) ajuda a identificar as ações privilegiadas que ocorreram na instância do Azure AD do usuário. Ações com privilégios incluem alterações de elevação (por exemplo, criação de funções ou redefinições de senha), alteração de configurações de política (por exemplo, políticas de senha) ou alterações na configuração de diretório (por exemplo, as alterações às configurações de federação de domínio).

Os relatórios fornecem o registro de auditoria para o nome do evento, o usuário que executou a ação, o recurso de destino afetado pela alteração e a data e hora (em UTC). Os usuários podem recuperar a lista de eventos de auditoria do Azure AD por meio do [portal do Azure](https://portal.azure.com/), conforme descrito em [Exibir os logs de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Os relatórios incluídos estão listados na tabela a seguir:

| Relatórios de segurança | Relatórios de atividades | Relatórios de auditoria |
| :--------------- | :--------------- | :------------ |
|Entradas de fontes desconhecidas| Uso do aplicativo: resumo| Relatório de auditoria de diretório|
|Entradas após várias falhas|  Uso do aplicativo: detalhado||
|Entradas de várias geografias|    Painel do aplicativo||
|Entradas de endereços IP com atividade suspeita|   Erros de provisionamento de conta||
|Atividades de entrada irregulares|    Dispositivos de usuário individual||
|Entradas de dispositivos possivelmente infectados|   Atividade de usuário individual||
|Usuários com atividade de entrada anômala| Relatório de atividade de grupos||
||Relatório de atividade de registro de redefinição de senha||
||Atividade de redefinição de senha|||

Os dados desses relatórios podem ser úteis para os aplicativos, como sistemas SIEM (Gerenciamento de Eventos e Informações de Segurança), auditoria e ferramentas de business intelligence. As APIs de relatório do Azure AD fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas [APIs](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) em várias ferramentas e linguagens de programação.

Os eventos no relatório de auditoria do Azure AD são mantidos por 180 dias.

> [!Note]
> Para saber mais sobre a retenção de relatórios, confira [Políticas de retenção de relatório do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Se você estiver interessado em manter os eventos de auditoria por mais tempo, use a API de Relatórios para efetuar o pull dos [eventos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) regularmente para um armazenamento de dados separado.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Logs de máquina virtual que usam o Diagnóstico do Azure

O [Diagnóstico do Azure](https://docs.microsoft.com/azure/azure-diagnostics) é a funcionalidade do Azure que habilita a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de qualquer uma das várias fontes. As compatíveis no momento são as [funções de trabalho e Web do serviço de nuvem do Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Logs de máquina virtual que usam o Diagnóstico do Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Máquinas virtuais do Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) que executam o Microsoft Windows e o [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

É possível habilitar o Diagnóstico do Azure em uma máquina virtual realizando uma da seguintes ações:

* [Usar o Visual Studio para rastrear máquinas virtuais do Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Configurar o Diagnóstico do Azure remotamente em uma máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Usar o PowerShell para configurar o diagnóstico em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando um modelo do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Análise de Armazenamento

A [Análise de Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) registra em log e fornece dados de métrica para uma conta de armazenamento. Você pode usar esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento. O log da Análise de Armazenamento está disponível para os [serviços de Armazenamento de Blobs, de Filas e de Tabelas do Azure](https://docs.microsoft.com/azure/storage/storage-introduction). A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento.

Você pode usar essas informações para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço. As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividades em seu ponto de extremidade de blob, mas não em seus pontos de extremidade de tabela ou fila, somente os logs pertencentes ao serviço de Armazenamento de Blobs serão criados.

Para usar a Análise de Armazenamento, habilite-a separadamente para cada serviço que deseja monitorar. Você pode habilitá-la no [portal do Azure](https://portal.azure.com/). Para obter mais informações, confira [Monitorar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use a operação Definir Propriedades do Serviço para habilitar o Storage Analytics individualmente em cada serviço.

Os dados agregados são armazenados em um blob conhecido (para registro em log) e em tabelas conhecidas (para métricas), que podem ser acessados usando as APIs de serviço de Armazenamento de Tabelas e de Blobs.

A Análise de Armazenamento tem um limite de 20 TB (terabyte) na quantidade de dados armazenados, que não depende do limite total da conta de armazenamento. Todos os logs são armazenados em [blobs de bloco](https://docs.microsoft.com/azure/storage/storage-analytics) em um contêiner denominado $logs, que é criado automaticamente quando você habilita a Análise de Armazenamento para uma conta de armazenamento.

> [!Note]
> * Para obter mais informações sobre cobrança e políticas de retenção de dados, confira [Análise de Armazenamento e cobrança](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Para obter mais informações sobre limites de contas de armazenamento, confira [Escalabilidade e metas de desempenho do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

A Análise de Armazenamento registra os seguintes tipos de solicitações anônimas e autenticadas:

| Autenticada  | Anônima|
| :------------- | :-------------|
| Solicitações bem-sucedidas | Solicitações bem-sucedidas |
|Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros | Solicitações que usam uma assinatura de acesso compartilhado, incluindo solicitações com êxito e com falha |
| Solicitações que usam uma assinatura de acesso compartilhado, incluindo solicitações com êxito e com falha |Erros de tempo limite para o cliente e para o servidor |
|   Solicitações de dados de análise |    Solicitações GET com falha com o código de erro 304 (não modificado) |
| As solicitações feitas pela própria análise de armazenamento, como criação de log ou exclusão, não estão conectadas. Uma lista completa dos dados registrados está documentada em [Mensagens de operações e status registradas da Análise de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [Formato do log da Análise de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Todas as outras solicitações anônimas com falha não estão conectadas. Uma lista completa dos dados registrados está documentada em [Mensagens de operações e status registradas da Análise de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [Formato do log da Análise de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Logs de rede do Azure

O log e monitoramento de rede no Azure é completo e abrange duas categorias amplas:

* [Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Monitoramento de rede baseado em cenário fornecido com os recursos do Observador de Rede. Esse serviço inclui a captura de pacotes, próximo salto, verificação do fluxo de IP, exibição do grupo de segurança e logs de fluxo de NSG. O monitoramento no nível do cenário fornece uma exibição completa dos recursos de rede em contraste com o monitoramento de recursos de rede individual.

* [Monitoramento de recursos](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): O monitoramento no nível do recurso é composto de quatro recursos: logs de diagnóstico, métricas, solução de problemas e integridade dos recursos. Todos esses recursos são compilados no nível do recurso da rede.

![Logs de rede do Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure.

### <a name="network-security-group-flow-logging"></a>Registro de fluxo de Grupo de Segurança de Rede

Os [logs de fluxo de NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) são um recurso do Observador de Rede que você pode usar para exibir informações sobre o tráfego IP de entrada e saída por meio de um NSG. Esses logs de fluxo são gravados no formato JSON e mostram:
* Fluxos de entrada e saída por regra.
* A NIC à qual o fluxo se aplica.
* Informações de cinco tuplas sobre o fluxo: o IP de origem ou de destino, a porta de origem ou de destino e o protocolo.
* Indica se o tráfego foi permitido ou negado.

Embora os logs de fluxo sejam destinados aos NSGs, eles não são exibidos da mesma forma como os outros logs. Os logs de fluxo são armazenados apenas em uma conta de armazenamento.

As mesmas políticas de retenção que são vistas em outros logs aplicam-se aos logs de fluxo. Os logs têm uma política de retenção que você pode definir de 1 dia até 365 dias. Se uma política de retenção não for definida, os logs serão mantidos para sempre.

**Logs de diagnóstico**

Os eventos periódicos e espontâneas são criados pelos recursos da rede e registrados nas contas de armazenamento e enviados para um hub de eventos ou Log Analytics. Os logs fornecem informações sobre a integridade de um recurso. Eles podem ser visualizados em ferramentas como o Power BI e o Log Analytics. Para saber como exibir os logs de diagnóstico, confira [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Logs de diagnóstico](./media/azure-log-audit/azure-log-audit-fig5.png)

Os logs de diagnóstico estão disponíveis para o [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), os [Grupos de Segurança da Rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), as Rotas e o [Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

O Observador de Rede fornece uma exibição dos logs de diagnóstico. Essa exibição contém todos os recursos de rede que oferecem suporte ao log de diagnóstico. Nessa exibição, você pode habilitar e desabilitar os recursos de rede de modo rápido e prático.


Além das funcionalidades de log mencionadas anteriormente, o Observador de Rede conta com as seguintes funcionalidades no momento:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Fornece uma exibição no nível da rede que mostra as diversas interconexões e associações entre os recursos de rede em um grupo de recursos.

- [Captura de Pacote Variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Captura os dados do pacote dentro e fora de uma máquina virtual. As opções avançadas de filtragem e os controles de ajuste fino, como as configurações de limitação de tempo e de tamanho, fornecem versatilidade. Os dados do pacote podem ser armazenados em um armazenamento de blobs ou no disco local no formato de arquivo *.cap*.

* [Verificação de fluxo de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Verifica se um pacote é permitido ou negado com base nos parâmetros do pacote com cinco tuplas das informações do fluxo (isto é, IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se o pacote for negado por um grupo de segurança, a regra e o grupo que negaram o pacote serão retornados.

* [Próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Determina o próximo salto para os pacotes encaminhados na malha de rede do Azure, de modo que você possa diagnosticar as rotas definidas pelo usuário configuradas incorretamente.

* [Vista de grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Obtém as regras de segurança efetivas e aplicadas que são usadas em uma VM.

* [Solução de problemas de conexão e do gateway de rede virtual](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Ajuda você a solucionar problemas de conexões e gateways de rede virtual.

* [Limites de assinatura da rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Permite exibir o uso dos recursos de rede em relação aos limites.

### <a name="application-insights"></a>Application Insights

O [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de APM para desenvolvedores da Web em várias plataformas. Use-o para monitorar aplicativos Web ativos. Ele detecta anomalias de desempenho automaticamente. Ele inclui ferramentas de análise avançadas para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo.

O Application Insights foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

Ele funciona com aplicativos em uma ampla variedade de plataformas incluindo .NET, Node.js e J2EE, estejam eles hospedados localmente ou na nuvem. Ele é integrado ao seu processo de DevOps e tem pontos de conexão com várias ferramentas de desenvolvimento.

![Diagrama do Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

O Application Insights é indicado para a equipe de desenvolvimento, para ajudá-lo a compreender como está o desempenho de seu aplicativo e como ele está sendo usado. Ele monitora:

* **Solicitar taxas, tempos de resposta e taxas de falha**: Descubra quais páginas são mais populares, em que momentos do dia e onde os usuários estão. Confira as páginas que têm melhor desempenho. Se as taxas de falha e os tempos de resposta ficam altos quando há mais solicitações, você pode ter um problema de alocação de recursos.

* **Taxas de dependência, tempos de resposta e taxas de falha**: Descubra se os serviços externos estão causando lentidão.

* **Exceções**: Analise as estatísticas agregadas ou escolha instâncias específicas e faça uma busca detalhada no rastreamento de pilha e nas solicitações relacionadas. A maioria das exceções de navegador e servidor são relatadas.

* **Exibição de página e desempenho do navegador**: Obtenha relatórios de navegadores dos usuários.

* **Chamadas AJAX**: Obtêm as taxas de falha, os tempos de resposta e as taxas de páginas da Web.

* **Contagens de seção e usuários**.

* **Contadores de desempenho**: Obtêm dados de seus computadores de servidor Linux ou Windows, como CPU, memória e uso da rede.

* **Diagnóstico de host**: Obtêm dados do Docker ou do Azure.

* **Logs de rastreamento de diagnóstico**: Obtêm dados do seu aplicativo para que você possa correlacionar eventos de rastreamento com solicitações.

* **Métricas e eventos personalizados**: Obtêm dados que você escreve em código de cliente ou servidor, para acompanhar os eventos de negócios, como itens vendidos ou vitórias.

A tabela a seguir lista e descreve cenários de integração:

| Cenário de integração | DESCRIÇÃO |
| --------------------- | :---------- |
|[Mapa do aplicativo](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Os componentes de seu aplicativo, com as principais métricas e alertas.||
|[Pesquisa de diagnóstico para dados da instância](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| pesquise e filtre eventos como solicitações, exceções, chamadas de dependência, rastreamentos de log e exibições de página.||
|[Metrics Explorer para dados agregados](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|explore, filtre e segmente dados agregados, como taxas de solicitações, falhas e exceções; tempos de resposta e tempos de carregamento de página.||
|[Painéis](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|faça um mashup de dados de vários recursos e compartilhe com outras pessoas. Excelente para aplicativos com vários componentes e para exibição contínua no ambiente de equipe.||
|[Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|quando implantar um novo build, acompanhe esses indicadores de desempenho quase em tempo real para verificar se tudo está funcionando conforme esperado.||
|[Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|responda perguntas difíceis sobre o desempenho e o uso do seu aplicativo usando essa poderosa linguagem de consulta.||
|[Alertas automáticos e manuais](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Alertas automáticos se adaptam aos padrões normais de telemetria do seu aplicativo e são disparados quando há algo fora do padrão normal. Você também pode definir alertas em níveis específicos de métricas padrão ou personalizadas.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Exiba dados de desempenho no código. Vá até o código dos rastreamentos de pilha.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integre as métricas de uso com outro business intelligence.||
|[API REST](https://dev.applicationinsights.io/)|Escreva o código para executar consultas em suas métricas e dados brutos.||
|[Exportação contínua](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Exportação em massa de dados brutos para armazenamento no momento de sua chegada.||

### <a name="azure-security-center-alerts"></a>Alertas da Central de Segurança do Azure

A detecção de ameaças da Central de Segurança do Azure funciona coletando informações de segurança de seus recursos do Azure, de rede e de soluções de parceiros conectados automaticamente. Ele analisa essas informações geralmente correlacionando informações de várias fontes para identificar ameaças. Os alertas de segurança são priorizados na Central de Segurança, juntamente com recomendações sobre como corrigir a ameaça. Para obter mais informações, confira [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Diagrama da Central de Segurança do Azure](./media/azure-log-audit/azure-log-audit-fig7.png)

A Central de Segurança emprega análise de segurança avançada, que vai além das abordagens baseadas em assinatura. Ela aplica avanços em dados grandes e tecnologias de [aprendizado de máquina](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) para avaliar eventos em toda a malha de nuvem. Dessa forma, ela detecta ameaças que seriam impossíveis de identificar usando abordagens manuais e prevendo a evolução dos ataques. Essas análises de segurança incluem:

* **Inteligência integrada contra ameaças**: Procura atores mal-intencionados conhecidos aplicando a inteligência global em ameaças de produtos e serviços da Microsoft, da DCU (Unidade de Crimes Digitais) da Microsoft, do MSRC (Microsoft Security Response Center) e de feeds externos.

* **Análise comportamental**: Aplica padrões conhecidos para descobrir os comportamentos mal-intencionados.

* **Detecção de anomalias**: Usa estatísticas de criação de perfil para criar uma linha de base histórica. Ela o alertará sobre desvios das linhas de base estabelecidas em conformidade com um vetor de possível ataque.

Muitas operações de segurança e equipes de resposta a incidentes contam com uma solução SIEM como ponto de partida para separação e investigação de alertas de segurança. Com a Integração de Logs do Azure, você pode sincronizar alertas da Central de Segurança e eventos de segurança da máquina virtual, coletados pelos logs de auditoria e diagnóstico do Azure, com sua solução SIEM ou Log Analytics quase em tempo real.

## <a name="log-analytics"></a>Log Analytics

O Log Analytics é um serviço no Azure que ajuda a coletar e analisar dados gerados por recursos nos ambientes locais e de nuvem. Ele fornece informações em tempo real usando a pesquisa integrada e painéis personalizados para analisar prontamente milhões de registros em todas as suas cargas de trabalho e todos os seus servidores, independentemente de sua localização física.

![Diagrama do Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

No centro no Log Analytics está o workspace do Log Analytics, que está hospedado no Azure. O Log Analytics coleta dados no workspace de fontes conectadas configurando fontes de dados e adicionando soluções à sua assinatura. As fontes de dados e as soluções criam tipos de registro diferentes, cada um com seu próprio conjunto de propriedades. Mas as fontes e as soluções ainda podem ser analisadas juntas em consultas para o workspace. Esse funcionalidade permite que você use as mesmas ferramentas e métodos para trabalhar com uma variedade de dados coletados por diversas fontes.

As fontes conectadas são os computadores e outros recursos que geram dados os coletados pelo Log Analytics. As fontes podem incluir os agentes instalados em computadores [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) que se conectam diretamente ou agentes em um [grupo de gerenciamento do System Center Operations Manager conectado](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). O Log Analytics também pode coletar dados da [conta de armazenamento do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Fontes de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) são os vários tipos de dados coletados de cada fonte conectada. Essas fontes incluem eventos e [dados de desempenho](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) de agentes [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) e Linux, além de fontes como [logs do IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) e [logs de texto personalizado](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Você configura cada fonte de dados que deseja coletar e a configuração é fornecida automaticamente para cada fonte conectada.

Há quatro maneiras de [coletar logs e métricas para os serviços do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):

* Direcionar o Diagnóstico do Azure para o Log Analytics (**Diagnóstico** na tabela a seguir)

* Diagnóstico do Azure para o Armazenamento do Azure e para o Log Analytics (**Armazenamento** na tabela a seguir)

* Conectores para serviços do Azure (**Conector** na tabela a seguir)

* Scripts para coletar e postar dados para o Log Analytics (células em branco na tabela a seguir e para os serviços que não estão listados)

| Serviço | Tipo de recurso | Logs | Métricas | Solução |
| :------ | :------------ | :--- | :------ | :------- |
|Gateway de Aplicativo do Azure| Microsoft.Network/<br>applicationGateways|  Diagnósticos|Diagnósticos|    [Análise de Gateway de](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Aplicativo do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Conector|  Conector|  [Conector do Application](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Insights (Visualização)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Contas de Automação do Azure| Microsoft.Automation/<br>AutomationAccounts|    Diagnósticos||       [Mais informações](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Contas do Lote do Azure|  Microsoft.Batch/<br>batchAccounts|  Diagnósticos|    Diagnósticos||
|Serviços de Nuvem clássicos||       Armazenamento||       [Mais informações](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Serviços Cognitivos|    Microsoft.CognitiveServices/<br>accounts|       Diagnósticos|||
|Análise Azure Data Lake| Microsoft.DataLakeAnalytics/<br>accounts|   Diagnósticos|||
|Repositório Azure Data Lake| Microsoft.DataLakeStore/<br>accounts|   Diagnósticos|||
|Namespace do hub de eventos do Azure| Microsoft.EventHub/<br>namespaces|  Diagnostics|    Diagnósticos||
|Hub IoT do Azure| Microsoft.Devices/<br>IotHubs||     Diagnósticos||
|Cofre da Chave do Azure|   Microsoft.KeyVault/<br>vaults|  Diagnósticos  || [Análise do Cofre de Chaves](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnósticos|||
|Aplicativos Lógicos do Azure|  Microsoft.Logic/<br>workflows|  Diagnósticos|    Diagnósticos||
||Microsoft.Logic/<br>integrationAccounts||||
|Grupos de segurança de rede|   Microsoft.Network/<br>networksecuritygroups|Diagnósticos||   [Análise do Grupo de Segurança de Rede do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Cofres de recuperação|   Microsoft.RecoveryServices/<br>vaults|||[Análise dos Serviços de Recuperação do Azure (Visualização)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Serviços Search|   Microsoft.Search/<br>searchServices|    Diagnósticos|    Diagnósticos||
|Namespace do Barramento de Serviço| Microsoft.ServiceBus/<br>namespaces|    Diagnostics|Diagnósticos|    [Análise do Barramento de Serviço (Visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Armazenamento||    [Análise do Service Fabric (visualização)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>databases||       Diagnósticos||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Armazenamento|||         Script| [Análise do Azure Storage (Visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Máquinas Virtuais do Azure|    Microsoft.Compute/<br>virtualMachines|  Extensão|  Extensão||
||||Diagnósticos||
|conjuntos de escala de máquina virtual|    Microsoft.Compute/<br>virtualMachines    ||Diagnósticos||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Farms do servidor Web|Microsoft.Web/<br>serverfarms||   Diagnósticos
|Sites|  Microsoft.Web/<br>sites ||      Diagnósticos|    [Mais informações](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integração de log com sistemas SIEM locais

Com a Integração de Logs do Azure, você pode integrar registros brutos de recursos do Azure a seu sistema de SIEM (gerenciamento de evento e informações de segurança) local. Downloads de AzLog foram desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer para prosseguir com a análise da postagem [Usar o Azure Monitor para a integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![Diagrama de Integração de Log](./media/azure-log-audit/azure-log-audit-fig9.png)

A Integração de Log coleta o Diagnóstico do Azure das Máquinas Virtuais do Windows, dos logs de atividades do Azure, dos alertas da Central de Segurança do Azure e dos logs do provedor de recursos do Azure. Esta integração fornece um painel unificado para todos os seus ativos, quer sejam locais ou na nuvem, para que você possa agregar, correlacionar, analisar e emitir alertas de eventos de segurança.

A Integração do Log dá suporte à integração dos logs de atividades do Azure, logs de eventos do Windows de Máquinas Virtuais do Windows com sua assinatura do Azure, alertas da Central de Segurança do Azure, logs de diagnóstico do Azure e logs de auditoria do Azure AD.

| Tipo de log | Log Analytics que dá suporte a JSON (Splunk, ArcSight e IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Logs de auditoria do Azure AD|   Sim|
|Logs de atividade| Sim|
|Alertas da Central de Segurança |Sim|
|Logs de diagnóstico (logs de recurso)|  Sim|
|Logs da VM|   Sim, por meio de eventos encaminhados e não por meio do JSON|

[Introdução à Integração de Logs do Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Este tutorial explica as etapas de instalação da Integração de Logs do Azure, bem como a integração de logs do armazenamento do Azure, logs de atividades do Azure, alertas da Central de Segurança do Azure e logs de auditoria do Microsoft Azure Active Directory.

Cenários de integração para SIEM:

* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Essa postagem de blog mostra a você como configurar a Integração de Logs do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar.

* [Perguntas frequentes sobre a Integração de Logs do Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Este artigo de perguntas frequentes responde às perguntas sobre a Integração de Logs do Azure.

* [Integração de alertas da Central de Segurança com a Integração de Logs do Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Este artigo discute como sincronizar os alertas da Central de Segurança, os eventos de segurança de máquina virtual coletados pelos logs de diagnóstico do Azure e os logs de auditoria do Azure com sua solução SIEM ou Log Analytics.

## <a name="next-steps"></a>Próximas etapas

- [Auditoria e log](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): Proteger dados, mantendo a visibilidade e respondendo rapidamente aos alertas de segurança em tempo hábil.

- [Log de segurança e coleta de logs de auditoria no Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Aplique essas configurações para ter certeza de que as instâncias do Azure estão coletando os logs de segurança e auditoria corretos.

- [Definir as configurações de auditoria de um conjunto de sites](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Se você for um administrador de uma coleção de sites, recupere o histórico de ações de usuários individuais e o histórico de ações realizadas durante um intervalo de datas específico. 

- [Pesquisar o log de auditoria no Centro de Segurança e Conformidade do Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Use o Centro de Segurança e Conformidade do Office 365 para pesquisar o log de auditoria unificado e exiba as atividades de usuário e do administrador em sua organização do Office 365.


