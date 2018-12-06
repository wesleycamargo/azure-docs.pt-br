---
title: Suporte à marca do Azure Resource Manager para recursos
description: Mostra quais tipos de recursos do Azure suportam tags. Fornece detalhes para todos os serviços do Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: a4bb423dc5eddde0fd2d2b9b4f263ab39dbd801f
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284975"
---
# <a name="tag-support-for-azure-resources"></a>Suporte a marcas para recursos do Azure
Este artigo descreve se dá suporte a um tipo de recurso [marcação](resource-group-using-tags.md).

## <a name="aad-domain-services"></a>Serviços de domínio do AAD
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| domains | Não  | 

## <a name="ad-hybrid-health-service"></a>Serviço de Integridade Híbrida do AD
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| services | Não  | 
| addsservices | Não  | 
| configuração | Não  | 
| agentes | Não  | 
| aadsupportcases | Não  | 
| relatórios | Não  | 
| servicehealthmetrics | Não  | 
| logs | Não  | 
| anonymousapiusers | Não  | 

## <a name="analysis-services"></a>Serviços de análise
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | SIM | 

## <a name="api-hubs"></a>Hubs de API
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| apiManagementAccounts | Não  | 
| apiManagementAccounts/connectionProviders | Não  | 
| apiManagementAccounts/connections | Não  | 
| apiManagementAccounts/connectionAcls | Não  | 
| apiManagementAccounts/connectionProviderAcls | Não  | 
| apiManagementAccounts/apis | Não  | 

## <a name="api-management"></a>Gerenciamento de API
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| propriedade serviço | SIM | 

## <a name="automation"></a>Automação
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| automationaccounts | SIM | 
| automationAccounts/runbooks | SIM | 
| automationAccounts/configurations | SIM | 
| automationAccounts/runbooks | Não  | 
| automationAccounts/configurations | Não  | 
| automationAccounts/runbooks | Não  | 

## <a name="batch"></a>Lote
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| batchAccounts | SIM | 

## <a name="batch-ai"></a>Lote AI
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| clusters | SIM | 
| jobs | SIM | 
| fileservers | SIM | 
| workspaces | SIM | 
| workspaces/clusters | Não  | 
| workspaces/clusters | Não  | 
| workspaces/experiments | Não  | 
| workspaces/experiments | Não  | 

## <a name="bing-maps"></a>Bing Mapas
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| mapApis | SIM | 

## <a name="biztalk-services"></a>Serviços BizTalk
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| BizTalk | SIM | 

## <a name="cache"></a>Cache
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Redis | SIM | 

## <a name="cdn"></a>CDN
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| perfis | SIM | 
| profiles/endpoints | SIM | 
| profiles/endpoints/origins | Não  | 
| profiles/endpoints/origins | Não  | 
| validateProbe | Não  | 
| edgenodes | Não  | 

## <a name="classic-compute"></a>Compute Clássico
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| domainNames | Não  | 
| domainNames/slots | Não  | 
| domainNames/slots | Não  | 
| virtualMachines | Não  | 
| virtualMachines/diagnosticSettings | Não  | 
| virtualMachines/metricDefinitions | Não  | 
| virtualMachines/metrics | Não  | 

## <a name="classic-infrastructure-migrate"></a>Migração de infraestrutura clássica
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Migração de infraestrutura clássica | Não  | 

## <a name="classic-network"></a>Rede Clássica
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| virtualNetworks | Não  | 
| virtualNetworks/virtualNetworkPeerings | Não  | 
| virtualNetworks/virtualNetworkPeerings | Não  | 

## <a name="classic-storage"></a>Armazenamento Clássico
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| storageAccounts/services | Não  | 
| storageAccounts/services/diagnosticSettings | Não  | 

## <a name="compute"></a>Computação
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| availabilitySets | SIM | 
| virtualMachines | SIM | 
| virtualMachines/extensions | SIM | 
| virtualMachineScaleSets | SIM | 
| virtualMachines/extensions | Não  | 
| virtualMachineScaleSets/virtualMachines | Não  | 
| virtualMachineScaleSets/networkInterfaces | Não  | 
| virtualMachineScaleSets/networkInterfaces | Não  | 
| virtualMachineScaleSets/publicIPAddresses | Não  | 
| restorePointCollections | SIM | 
| restorePointCollections/restorePoints | Não  | 
| virtualMachines/diagnosticSettings | Não  | 
| virtualMachines/metricDefinitions | Não  | 
| sharedVMImages | SIM | 
| sharedVMImages/versions | SIM | 
| disks | SIM | 
| snapshots | SIM | 
| images | SIM | 

## <a name="container"></a>Contêiner
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| containerGroups | SIM | 

## <a name="container-instance"></a>Instância de contêiner
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| containerGroups | SIM | 
| serviceAssociationLinks | Não  | 

## <a name="container-service"></a>Serviço de Contêiner
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| containerServices | SIM | 

## <a name="cortana-analytics"></a>Cortana Analytics
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | SIM | 

## <a name="cosmos-db"></a>Cosmos DB
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| databaseAccounts | SIM | 
| databaseAccounts | Não  | 

## <a name="cost-management"></a>Gerenciamento de Custos
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Conectores | SIM | 

## <a name="data-box-edge"></a>Data Box Edge
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| DataBoxEdgeDevices | SIM | 

## <a name="data-catalog"></a>Catálogo de Dados
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| catalogs | SIM | 

## <a name="data-connect"></a>Conexão de dados
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| connectionManagers | SIM | 

## <a name="data-factory"></a>Data Factory
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| dataFactories | SIM | 
| factories | SIM | 
| factories/integrationRuntimes | Não  | 
| dataFactories/diagnosticSettings | Não  | 
| dataFactories/metricDefinitions | Não  | 
| dataFactorySchema | Não  | 

## <a name="devices"></a>Dispositivos
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| IotHubs | SIM | 
| IotHubs/eventGridFilters | Não  | 
| ProvisioningServices | SIM | 

## <a name="devspaces"></a>Devspaces
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| controladores | SIM | 

## <a name="devtest-lab"></a>Laboratório de Desenvolvimento/Teste
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| labs | SIM | 
| schedules | SIM | 
| labs/virtualMachines | SIM | 
| labs/serviceRunners | SIM | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| lcsprojects | Não  | 
| lcsprojects/connectors | Não  | 
| lcsprojects/clouddeployments | Não  | 

## <a name="event-grid"></a>Grade de Eventos
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| eventSubscriptions | Não  | 
| topics | SIM | 
| domains | SIM | 
| domains/topics | Não  | 
| topicTypes | Não  | 
| extensionTopics | Não  | 

## <a name="event-hub"></a>Hub de evento
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| namespaces | SIM | 
| clusters | SIM | 

## <a name="hana-on-azure"></a>Hana no Azure
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| hanaInstances | SIM | 

## <a name="hdinsight"></a>HDInsight
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| clusters | SIM | 
| clusters/applications | Não  | 

## <a name="import-export"></a>Importação/Exportação
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| jobs | SIM | 

## <a name="insights"></a>Insights
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| components | SIM | 
| components/query | Não  | 
| components/metrics | Não  | 
| components/metrics | Não  | 
| webtests | SIM | 
| consultas | Não  | 
| scheduledqueryrules | SIM | 
| components/pricingPlans | Não  | 
| migrateToNewPricingModel | Não  | 
| rollbackToLegacyPricingModel | Não  | 
| automatedExportSettings | Não  | 
| workbooks | SIM | 
| myWorkbooks | Não  | 
| logs | Não  | 

## <a name="key-vault"></a>Key Vault
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| vaults | SIM | 
| vaults/secrets | Não  | 
| vaults/accessPolicies | Não  | 
| deletedVaults | Não  | 

## <a name="log-analytics"></a>Log Analytics
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| logs | Não  | 

## <a name="logic"></a>Lógica
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| workflows | SIM | 
| integrationAccounts | SIM | 

## <a name="machine-learning-services"></a>Serviços de Machine Learning
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| workspaces | SIM | 
| workspaces/computes | Não  | 

## <a name="managed-identity"></a>Identidade Gerenciada
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Identidades | Não  | 
| userAssignedIdentities | SIM | 

## <a name="mariadb"></a>MariaDB
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | SIM | 
| servers/recoverableServers | Não  | 
| servers/virtualNetworkRules | Não  | 

## <a name="marketplace-apps"></a>Apps do Marketplace
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| classicDevServices | SIM | 

## <a name="marketplace-ordering"></a>Encomenda do Marketplace
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Contratos | Não  | 
| offertypes | Não  | 

## <a name="media"></a>Mídia
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| mediaservices | SIM | 
| mediaservices/assets | Não  | 
| mediaservices/contentKeyPolicies | Não  | 
| mediaservices/streamingLocators | Não  | 
| mediaservices/streamingLocators | Não  | 
| mediaservices/eventGridFilters | Não  | 
| mediaservices/transforms | Não  | 
| mediaservices/transforms | Não  | 
| mediaservices/streamingEndpoints | SIM | 
| mediaservices/liveEvents | SIM | 
| mediaservices/liveEvents/liveOutputs | Não  | 
| mediaservices/streamingEndpoints | Não  | 
| mediaservices/liveEvents | Não  | 
| mediaservices/liveEvents | Não  | 
| mediaservices/assets/assetFilters | Não  | 
| mediaservices/accountFilters | Não  | 

## <a name="mysql"></a>MySQL
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | SIM | 
| servers/recoverableServers | Não  | 
| servers/virtualNetworkRules | Não  | 

## <a name="network"></a>Rede
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| virtualNetworks | SIM | 
| publicIPAddresses | SIM | 
| networkInterfaces | SIM | 
| interfaceEndpoints | SIM | 
| loadBalancers | SIM | 
| networkSecurityGroups | SIM | 
| applicationSecurityGroups | SIM | 
| serviceEndpointPolicies | SIM | 
| networkIntentPolicies | SIM | 
| routeTables | SIM | 
| publicIPPrefixes | SIM | 
| networkWatchers | SIM | 
| networkWatchers/connectionMonitors | SIM | 
| networkWatchers/lenses | SIM | 
| networkWatchers/lenses | SIM | 
| virtualNetworkGateways | SIM | 
| virtualNetworkGateways | SIM | 
| connections | SIM | 
| applicationGateways | SIM | 
| expressRouteCircuits | SIM | 
| routeFilters | SIM | 
| virtualWans | SIM | 
| vpnSites | SIM | 
| virtualWans | SIM | 
| vpnGateways | SIM | 
| azureFirewalls | SIM | 
| virtualNetworkTaps | SIM | 
| privateLinkServices | SIM | 
| ddosProtectionPlans | SIM | 
| networkProfiles | SIM | 
| frontdoors | SIM | 
| frontdoorWebApplicationFirewallPolicies | SIM | 
| frontdoorWebApplicationFirewallPolicies | SIM | 

## <a name="notification-hubs"></a>Hubs de Notificação
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| namespaces | SIM | 
| namespaces/notificationHubs | SIM | 

## <a name="portal"></a>Portal
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| dashboards | SIM | 

## <a name="portal-sdk"></a>Portal SDK
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| rootResources | SIM | 

## <a name="postgresql"></a>PostgreSQL
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | SIM | 
| servers/recoverableServers | Não  | 
| servers/virtualNetworkRules | Não  | 
| servers/topQueryStatistics | Não  | 
| servers/queryTexts | Não  | 
| servers/waitStatistics | Não  | 
| servers/advisors | Não  | 

## <a name="power-bi"></a>Power BI
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| workspaceCollections | SIM | 

## <a name="recovery-services"></a>Serviços de Recuperação
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| vaults | SIM | 
| backupProtectedItems | Não  | 

## <a name="relay"></a>Retransmissão
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| namespaces | SIM | 

## <a name="resources"></a>Recursos
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| resourceGroups | SIM | 
| subscriptions/resourceGroups | SIM | 

## <a name="scheduler"></a>Agendador
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| jobcollections | SIM | 
| flows | SIM | 

## <a name="search"></a>Search
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| searchServices | SIM | 
| resourceHealthMetadata | Não  | 

## <a name="security"></a>Segurança
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| dataCollectionAgents | Não  | 

## <a name="service-bus"></a>Barramento de Serviço
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| namespaces | SIM | 
| namespaces/eventgridfilters | Não  | 

## <a name="service-fabric"></a>Service Fabric
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| clusters | SIM | 
| clusters/applications | Não  | 

## <a name="service-fabric-mesh"></a>Malha do Service Fabric
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| de dimensionamento da Web | SIM | 
| networks | SIM | 
| volumes | SIM | 

## <a name="signalr-service"></a>Serviço SignalR
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| SignalR | SIM | 

## <a name="site-recovery"></a>Site Recovery
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| SiteRecoveryVault | SIM | 

## <a name="solutions"></a>Soluções
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| de dimensionamento da Web | SIM | 
| applicationDefinitions | SIM | 
| jitRequests | SIM | 

## <a name="sql-virtual-machine"></a>Máquina virtual SQL
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| DWVM | SIM | 

## <a name="storage"></a>Armazenamento
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| storageAccounts | SIM | 
| storageAccounts/blobServices | Não  | 
| storageAccounts/blobServices | Não  | 
| storageAccounts/blobServices | Não  | 
| storageAccounts/blobServices | Não  | 
| storageAccounts/services | Não  | 
| storageAccounts/services/metricDefinitions | Não  | 

## <a name="storage-sync"></a>Armazenamento Síncrono
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| storageSyncServices | SIM | 
| storageSyncServices/syncGroups | Não  | 
| storageSyncServices/syncGroups/cloudEndpoints | Não  | 
| storageSyncServices/syncGroups/cloudEndpoints | Não  | 
| storageSyncServices/registeredServers | Não  | 
| storageSyncServices/workflows | Não  | 

## <a name="storsimple"></a>Storsimple
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| managers | SIM | 

## <a name="stream-analytics"></a>Stream Analytics
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| streamingjobs | SIM | 
| streamingjobs/diagnosticSettings | Não  | 
| streamingjobs/metricDefinitions | Não  | 

## <a name="subscription"></a>Assinatura
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| SubscriptionDefinitions | Não  | 
| SubscriptionDefinitions | Não  | 

## <a name="support"></a>Suporte
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| supporttickets | Não  | 

## <a name="visual-studio"></a>Visual Studio
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| conta | SIM | 
| account/project | SIM | 
| account/extension | SIM | 
| conta | SIM | 
| account/project | SIM | 
| account/extension | SIM | 

## <a name="web"></a>Web
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| sites/instances | Não  | 
| sites/instances | Não  | 
| sites/instances/extensions | Não  | 
| sites/instances/extensions | Não  | 
| publishingUsers | Não  | 
| validade | Não  | 
| sourceControls | Não  | 
| sites/hostNameBindings | Não  | 
| sites/domainOwnershipIdentifiers | Não  | 
| sites/hostNameBindings | Não  | 
| certificates | SIM | 
| serverFarms | SIM | 
| serverFarms/workers | Não  | 
| sites | SIM | 
| sites/slots | SIM | 
| sites/metrics | Não  | 
| sites/slots/metrics | Não  | 
| sites/premieraddons | SIM | 
| hostingEnvironments | SIM | 
| hostingEnvironments/multiRolePools | Não  | 
| hostingEnvironments/multiRolePools | Não  | 
| hostingEnvironments/multiRolePools | Não  | 
| funções | Não  | 
| deletedSites | Não  | 
| apiManagementAccounts | Não  | 
| apiManagementAccounts/connections | Não  | 
| apiManagementAccounts/connectionAcls | Não  | 
| apiManagementAccounts/apis/connections/connectionAcls | Não  | 
| apiManagementAccounts/apis/connections/connectionAcls | Não  | 
| apiManagementAccounts/apis/connections/connectionAcls | Não  | 
| apiManagementAccounts/apis/connections/connectionAcls | Não  | 
| apiManagementAccounts/apis | Não  | 
| apiManagementAccounts/apis/localizedDefinitions | Não  | 
| apiManagementAccounts/apis/connections/connectionAcls | Não  | 
| connections | SIM | 
| customApis | SIM | 
| connectionGateways | SIM | 
| billingMeters | Não  | 
| verifyHostingEnvironmentVnet | Não  | 

## <a name="xrm"></a>XRM
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| organizações | Não  | 

## <a name="next-steps"></a>Próximas etapas
Para saber como aplicar tags a recursos, consulte [Use tags para organizar seus recursos do Azure](resource-group-using-tags.md).
