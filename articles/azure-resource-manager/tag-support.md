---
title: Suporte à marca do Azure Resource Manager para recursos
description: Mostra quais tipos de recursos do Azure suportam tags. Fornece detalhes para todos os serviços do Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/02/2019
ms.author: tomfitz
ms.openlocfilehash: 50ea7a2446b5560bd208b2da128fa877068ce452
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000284"
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
| addsservices | Não  |
| aadsupportcases | Não  | 
| agentes | Não  | 
| anonymousapiusers | Não  | 
| configuração | Não  | 
| logs | Não  | 
| relatórios | Não  | 
| services | Não  | 
| servicehealthmetrics | Não  | 

## <a name="aks"></a>AKS
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| managedClusters | SIM | 

## <a name="analysis-services"></a>Serviços de análise
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | SIM | 

## <a name="api-hubs"></a>Hubs de API
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| apiManagementAccounts | Não  | 
| apiManagementAccounts/apis | Não  | 
| apiManagementAccounts/connectionAcls | Não  | 
| apiManagementAccounts/connectionProviders | Não  | 
| apiManagementAccounts/connectionProviderAcls | Não  | 
| apiManagementAccounts/connections | Não  | 

## <a name="api-management"></a>Gerenciamento de API
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| propriedade serviço | SIM | 

## <a name="automation"></a>Automação
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| automationaccounts | SIM | 
| automationAccounts/configurations | SIM | 
| automationAccounts/runbooks | Não  | 
| automationAccounts/runbooks | SIM | 
| automationAccounts/configurations | Não  | 
| automationAccounts/runbooks | Não  | 

## <a name="azure-database-for-mariadb"></a>Banco de Dados do Azure para MariaDB
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | SIM | 
| servers/configurations | Não  |
| servers/databases | Não  |
| servers/firewallRules | Não  |
| servers/recoverableServers | Não  | 
| servers/securityAlertPolicies | Não  |
| servers/virtualNetworkRules | Não  | 

## <a name="azure-database-for-mysql"></a>Banco de Dados do Azure para MySQL
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | SIM | 
| servers/configurations | Não  |
| servers/databases | Não  |
| servers/firewallRules | Não  |
| servers/recoverableServers | Não  | 
| servers/securityAlertPolicies | Não  |
| servers/virtualNetworkRules | Não  | 

## <a name="azure-database-for-postgresql"></a>Banco de Dados do Azure para PostgreSQL
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | SIM | 
| servers/advisors | Não  | 
| servers/configurations | Não  |
| servers/databases | Não  |
| servers/firewallRules | Não  |
| servers/queryTexts | Não  | 
| servers/recoverableServers | Não  | 
| servers/securityAlertPolicies | Não  |
| servers/topQueryStatistics | Não  | 
| servers/virtualNetworkRules | Não  | 
| servers/waitStatistics | Não  | 

## <a name="batch"></a>Lote
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| batchAccounts | SIM | 

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
| edgenodes | Não  | 
| perfis | SIM | 
| profiles/endpoints | SIM | 
| profiles/endpoints/origins | Não  | 
| profiles/endpoints/origins | Não  | 
| validateProbe | Não  | 

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
| disks | SIM | 
| images | SIM | 
| restorePointCollections | SIM | 
| restorePointCollections/restorePoints | Não  | 
| sharedVMImages | SIM | 
| sharedVMImages/versions | SIM | 
| snapshots | SIM | 
| virtualMachines | SIM | 
| virtualMachines/diagnosticSettings | Não  | 
| virtualMachines/extensions | SIM | 
| virtualMachines/metricDefinitions | Não  | 
| virtualMachineScaleSets | SIM | 
| virtualMachines/extensions | Não  | 
| virtualMachineScaleSets/networkInterfaces | Não  | 
| virtualMachineScaleSets/publicIPAddresses | Não  | 
| virtualMachineScaleSets/virtualMachines | Não  | 
| virtualMachineScaleSets/networkInterfaces | Não  | 

## <a name="container"></a>Contêiner
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| containerGroups | SIM | 

## <a name="container-instance"></a>Instância de contêiner
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| containerGroups | SIM | 
| serviceAssociationLinks | Não  | 

## <a name="container-registry"></a>Registro de Contêiner
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| registries | SIM | 
| registries/replications | SIM |
| registries/tasks | SIM |
| registries/webhooks | SIM |

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

## <a name="data-box"></a>Data Box
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| jobs | SIM | 

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
| dataFactories/diagnosticSettings | Não  | 
| dataFactories/metricDefinitions | Não  | 
| dataFactorySchema | Não  | 
| factories | SIM | 
| factories/integrationRuntimes | Não  | 

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
| labs/artifactsources | SIM |
| labs/costs | SIM |
| labs/customimages | SIM |
| labs/formulas | SIM |
| labs/notificationchannels | SIM |
| labs/policysets/policies | SIM |
| labs/schedules | SIM |
| labs/serviceRunners | SIM | 
| labs/users | SIM |
| labs/users/disks | SIM |
| labs/users/environments | SIM |
| labs/users/secrets | SIM |
| labs/users/servicefabrics | SIM |
| labs/users/servicefabrics/schedules | SIM |
| labs/virtualMachines | SIM | 
| labs/virtualmachines/schedules | SIM |
| labs/virtualnetworks | SIM |
| schedules | SIM | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| lcsprojects | Não  | 
| lcsprojects/connectors | Não  | 
| lcsprojects/clouddeployments | Não  | 

## <a name="event-grid"></a>Grade de Eventos
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| domains | SIM | 
| domains/topics | Não  | 
| eventSubscriptions | Não  | 
| extensionTopics | Não  | 
| topics | SIM | 
| topicTypes | Não  | 

## <a name="event-hub"></a>Hub de evento
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| clusters | SIM | 
| namespaces | SIM | 
| namespaces/AuthorizationRules | Não  |
| namespaces/disasterRecoveryConfigs | Não  |
| namespaces/eventhubs | Não  |
| namespaces/eventhubs/authorizationRules | Não  |
| namespaces/eventhubs/consumergroups | Não  |

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
| actionGroups | SIM |
| activityLogAlerts | SIM |
| alertrules | SIM |
| automatedExportSettings | Não  | 
| components | SIM | 
| components/metrics | Não  | 
| components/metrics | Não  | 
| components/pricingPlans | Não  | 
| components/query | Não  | 
| logs | Não  | 
| metricAlerts | SIM |
| migrateToNewPricingModel | Não  | 
| myWorkbooks | Não  | 
| consultas | Não  | 
| rollbackToLegacyPricingModel | Não  | 
| scheduledqueryrules | SIM | 
| webtests | SIM | 
| workbooks | SIM | 

## <a name="key-vault"></a>Key Vault
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| deletedVaults | Não  | 
| vaults | SIM | 
| vaults/accessPolicies | Não  | 
| vaults/secrets | Não  | 

## <a name="log-analytics"></a>Log Analytics
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| logs | Não  | 

## <a name="logic"></a>Lógica
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| integrationAccounts | SIM | 
| workflows | SIM | 

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
| mediaservices/accountFilters | Não  | 
| mediaservices/assets | Não  | 
| mediaservices/assets/assetFilters | Não  | 
| mediaservices/contentKeyPolicies | Não  | 
| mediaservices/eventGridFilters | Não  | 
| mediaservices/liveEvents | Não  | 
| mediaservices/liveEvents | SIM | 
| mediaservices/liveEvents/liveOutputs | Não  | 
| mediaservices/liveEvents | Não  | 
| mediaservices/streamingEndpoints | SIM | 
| mediaservices/streamingEndpoints | Não  | 
| mediaservices/streamingLocators | Não  | 
| mediaservices/streamingLocators | Não  | 
| mediaservices/transforms | Não  | 
| mediaservices/transforms | Não  | 

## <a name="network"></a>Rede
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| applicationGateways | SIM | 
| applicationSecurityGroups | SIM | 
| azureFirewalls | SIM | 
| connections | SIM | 
| ddosProtectionPlans | SIM | 
| expressRouteCircuits | SIM | 
| frontdoors | SIM | 
| frontdoorWebApplicationFirewallPolicies | SIM | 
| interfaceEndpoints | SIM | 
| loadBalancers | SIM | 
| virtualNetworkGateways | SIM | 
| networkIntentPolicies | SIM | 
| networkInterfaces | SIM | 
| networkProfiles | SIM | 
| networkSecurityGroups | SIM | 
| networkWatchers | SIM | 
| networkWatchers/connectionMonitors | SIM | 
| networkWatchers/lenses | SIM | 
| networkWatchers/lenses | SIM | 
| privateLinkServices | SIM | 
| publicIPAddresses | SIM | 
| publicIPPrefixes | SIM | 
| routeFilters | SIM | 
| routeTables | SIM | 
| serviceEndpointPolicies | SIM | 
| virtualWans | SIM | 
| virtualNetworks | SIM | 
| virtualNetworkGateways | SIM | 
| virtualNetworkTaps | SIM | 
| virtualWans | SIM | 
| vpnGateways | SIM | 
| vpnSites | SIM | 
| frontdoorWebApplicationFirewallPolicies | SIM | 

## <a name="notification-hubs"></a>Hubs de Notificação
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| namespaces | SIM | 
| namespaces/notificationHubs | SIM | 

## <a name="operational-insights"></a>Insights Operacionais
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| workspaces | SIM |
| workspaces/dataSources | SIM |
| workspaces/linkedServices | SIM |
| workspaces/savedSearches | Não  |
| workspaces/storageInsightConfigs | SIM |

## <a name="operations-management"></a>Gerenciamento de Operações
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| solutions | Não  |

## <a name="portal"></a>Portal
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| dashboards | SIM | 

## <a name="portal-sdk"></a>Portal SDK
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| rootResources | SIM | 

## <a name="power-bi"></a>Power BI
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| workspaceCollections | SIM | 

## <a name="recovery-services"></a>Serviços de Recuperação
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| backupProtectedItems | Não  | 
| vaults | SIM | 

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
| resourceHealthMetadata | Não  | 
| searchServices | SIM | 

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

## <a name="sql"></a>SQL
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| locations/instanceFailoverGroups | Não  |
| managedInstances | SIM |
| managedInstances/databases | SIM |
| managedInstances/databases/backupShortTermRetentionPolicies | Não  |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não  |
| managedInstances/databases/vulnerabilityAssessments | Não  |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não  |
| managedInstances/encryptionProtector | Não  |
| managedInstances/keys | Não  |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não  |
| managedInstances/vulnerabilityAssessments | Não  |
| servers | SIM |
| servers/administrators | Não  |
| servers/advisors | Não  |
| servers/auditingSettings | Não  |
| servers/backupLongTermRetentionVaults | Não  |
| servers/communicationLinks | Não  |
| servers/connectionPolicies | Não  |
| servers/databases | SIM |
| servers/databases/advisors | Não  |
| servers/databases/auditingSettings | Não  |
| servers/databases/backupLongTermRetentionPolicies | Não  |
| servers/databases/backupShortTermRetentionPolicies | Não  |
| servers/databases/connectionPolicies | Não  |
| servers/databases/dataMaskingPolicies | Não  |
| servers/databases/dataMaskingPolicies/rules | Não  |
| servers/databases/extendedAuditingSettings | Não  |
| servers/databases/extensions | Não  |
| servers/databases/geoBackupPolicies | Não  |
| servers/databases/schemas/tables/columns/sensitivityLabels | Não  |
| servers/databases/securityAlertPolicies | Não  |
| servers/databases/syncGroups | Não  |
| servers/databases/syncGroups/syncMembers | Não  |
| servers/databases/transparentDataEncryption | Não  |
| servers/databases/vulnerabilityAssessments | Não  |
| servers/databases/vulnerabilityAssessments/rules/baselines | Não  |
| servers/disasterRecoveryConfiguration | Não  |
| servers/dnsAliases | Não  |
| servers/elasticPools | SIM |
| servers/encryptionProtector | Não  |
| servers/extendedAuditingSettings | Não  |
| servers/failoverGroups | SIM |
| servers/firewallRules | Não  |
| servers/jobAgents | SIM |
| servers/jobAgents/credentials | Não  |
| servers/jobAgents/jobs | Não  |
| servers/jobAgents/jobs/executions | Não  |
| servers/jobAgents/jobs/steps | Não  |
| servers/jobAgents/targetGroups | Não  |
| servers/keys | Não  |
| servers/securityAlertPolicies | Não  |
| servers/syncAgents | Não  |
| servers/virtualNetworkRules | Não  |
| servers/vulnerabilityAssessments | Não  |

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
| storageAccounts/services | Não  | 
| storageAccounts/services/metricDefinitions | Não  | 
| storageAccounts/blobServices | Não  | 

## <a name="storage-sync"></a>Armazenamento Síncrono
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| storageSyncServices | SIM | 
| storageSyncServices/registeredServers | Não  | 
| storageSyncServices/syncGroups | Não  | 
| storageSyncServices/syncGroups/cloudEndpoints | Não  | 
| storageSyncServices/syncGroups/cloudEndpoints | Não  | 
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
| account/extension | SIM | 
| account/project | SIM | 

## <a name="web"></a>Web
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| apiManagementAccounts | Não  | 
| apiManagementAccounts/apis/connections/connectionAcls | Não  | 
| apiManagementAccounts/apis | Não  | 
| apiManagementAccounts/apis/connections/connectionAcls | Não  | 
| apiManagementAccounts/apis/connections/connectionAcls | Não  | 
| apiManagementAccounts/apis/connections/connectionAcls | Não  | 
| apiManagementAccounts/apis/connections/connectionAcls | Não  | 
| apiManagementAccounts/apis/localizedDefinitions | Não  | 
| apiManagementAccounts/connectionAcls | Não  | 
| apiManagementAccounts/connections | Não  | 
| billingMeters | Não  | 
| certificates | SIM | 
| connectionGateways | SIM | 
| connections | SIM | 
| customApis | SIM | 
| deletedSites | Não  | 
| funções | Não  | 
| hostingEnvironments | SIM | 
| hostingEnvironments/multiRolePools | Não  | 
| hostingEnvironments/multiRolePools | Não  | 
| hostingEnvironments/multiRolePools | Não  | 
| publishingUsers | Não  | 
| serverFarms | SIM | 
| serverFarms/workers | Não  | 
| sites | SIM | 
| sites/domainOwnershipIdentifiers | Não  | 
| sites/hostNameBindings | Não  | 
| sites/instances | Não  | 
| sites/instances/extensions | Não  | 
| sites/metrics | Não  | 
| sites/premieraddons | SIM | 
| sites/slots | SIM | 
| sites/hostNameBindings | Não  | 
| sites/instances | Não  | 
| sites/instances/extensions | Não  | 
| sites/slots/metrics | Não  | 
| sourceControls | Não  | 
| validade | Não  | 
| verifyHostingEnvironmentVnet | Não  | 

## <a name="xrm"></a>XRM
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| organizações | Não  | 

## <a name="next-steps"></a>Próximas etapas
Para saber como aplicar tags a recursos, consulte [Use tags para organizar seus recursos do Azure](resource-group-using-tags.md).
