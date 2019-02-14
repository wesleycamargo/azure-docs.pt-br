---
title: Suporte à marca do Azure Resource Manager para recursos
description: Mostra quais tipos de recursos do Azure suportam tags. Fornece detalhes para todos os serviços do Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 1/24/2019
ms.author: tomfitz
ms.openlocfilehash: d9892b158eae1a83ab1f8eed6c14224e1d4bca15
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509899"
---
# <a name="tag-support-for-azure-resources"></a>Suporte a marcas para recursos do Azure
Este artigo descreve se um tipo de recurso dá suporte a [marcas](resource-group-using-tags.md).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| DomainServices | Sim | 
| DomainServices/oucontainer | Não  | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| diagnosticSettings | Não  | 
| diagnosticSettingsCategories | Não  | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| supportProviders | Não  | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| aadsupportcases | Não  | 
| addsservices | Não  | 
| agentes | Não  | 
| anonymousapiusers | Não  | 
| configuração | Não  | 
| logs | Não  | 
| relatórios | Não  | 
| services | Não  | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| configurações | Não  | 
| generateRecommendations | Não  | 
| filmes | Não  | 
| suppressions | Não  | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| actionRules | Não  | 
| alertas | Não  | 
| alertsList | Não  | 
| alertsSummary | Não  | 
| alertsSummaryList | Não  | 
| smartDetectorAlertRules | Não  | 
| smartDetectorRuntimeEnvironments | Não  | 
| smartGroups | Não  | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | Sim | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| reportFeedback | Não  | 
| propriedade serviço | Sim | 
| validateServiceName | Não  | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| attestationProviders | Não  | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| classicAdministrators | Não  | 
| denyAssignments | Não  | 
| elevateAccess | Não  | 
| locks | Não  | 
| permissões | Não  | 
| policyAssignments | Não  | 
| policyDefinitions | Não  | 
| policySetDefinitions | Não  | 
| providerOperations | Não  | 
| roleAssignments | Não  | 
| roleDefinitions | Não  | 

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| automationaccounts | Sim | 
| automationAccounts/configurations | Sim | 
| automationAccounts/runbooks | Não  | 
| automationAccounts/runbooks | Sim | 
| automationAccounts/configurations | Não  | 
| automationAccounts/runbooks | Não  | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| environments | Não  | 
| environments/accounts | Não  | 
| environments/accounts/namespaces | Não  | 
| environments/accounts/namespaces/configurations | Não  | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| b2cDirectories | Sim | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| registrations | Sim | 
| registrations/customerSubscriptions | Não  | 
| registrations/products | Não  | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| batchAccounts | Sim | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| billingAccounts | Não  | 
| billingAccounts/billingProfiles | Não  | 
| billingAccounts/billingProfiles/billingSubscriptions | Não  | 
| billingAccounts/billingProfiles/invoices | Não  | 
| billingAccounts/billingProfiles/invoices/pricesheet | Não  | 
| billingAccounts/billingProfiles/operationStatus | Não  | 
| billingAccounts/billingProfiles/paymentMethods | Não  | 
| billingAccounts/billingProfiles/policies | Não  | 
| billingAccounts/billingProfiles/pricesheet | Não  | 
| billingAccounts/billingProfiles/products | Não  | 
| billingAccounts/billingProfiles/transactions | Não  | 
| billingAccounts/billingSubscriptions | Não  | 
| billingAccounts/departments | Não  | 
| billingAccounts/eligibleOffers | Não  | 
| billingAccounts/enrollmentAccounts | Não  | 
| billingAccounts/invoices | Não  | 
| billingAccounts/invoiceSections | Não  | 
| billingAccounts/invoiceSections/billingSubscriptions | Não  | 
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Não  | 
| billingAccounts/invoiceSections/importRequests | Não  | 
| billingAccounts/invoiceSections/initiateImportRequest | Não  | 
| billingAccounts/invoiceSections/initiateTransfer | Não  | 
| billingAccounts/invoiceSections/operationStatus | Não  | 
| billingAccounts/invoiceSections/products | Não  | 
| billingAccounts/invoiceSections/transfers | Não  | 
| billingAccounts/products | Não  | 
| billingAccounts/projects | Não  | 
| billingAccounts/projects/billingSubscriptions | Não  | 
| billingAccounts/projects/importRequests | Não  | 
| billingAccounts/projects/initiateImportRequest | Não  | 
| billingAccounts/projects/operationStatus | Não  | 
| billingAccounts/projects/products | Não  | 
| billingAccounts/transactions | Não  | 
| billingPeriods | Não  | 
| BillingPermissions | Não  | 
| billingProperty | Não  | 
| BillingRoleAssignments | Não  | 
| BillingRoleDefinitions | Não  | 
| CreateBillingRoleAssignment | Não  | 
| departments | Não  | 
| enrollmentAccounts | Não  | 
| importRequests | Não  | 
| importRequests/acceptImportRequest | Não  | 
| importRequests/declineImportRequest | Não  | 
| invoices | Não  | 
| transfers | Não  | 
| transfers/acceptTransfer | Não  | 
| transfers/declineTransfer | Não  | 
| transfers/operationStatus | Não  | 
| usagePlans | Não  | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| mapApis | Sim | 
| updateCommunicationPreference | Não  | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| BizTalk | Sim | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| blueprintAssignments | Não  | 
| blueprintAssignments/assignmentOperations | Não  | 
| blueprintAssignments/operations | Não  | 
| blueprints | Não  | 
| blueprints/artifacts | Não  | 
| blueprints/versions | Não  | 
| blueprints/versions/artifacts | Não  | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| botServices | Sim | 
| botServices/channels | Não  | 
| botServices/connections | Não  | 

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Redis | Sim | 
| RedisConfigDefinition | Não  | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| appliedReservations | Não  | 
| calculatePrice | Não  | 
| catalogs | Não  | 
| commercialReservationOrders | Não  | 
| reservationOrders | Não  | 
| reservationOrders/calculateRefund | Não  | 
| reservationOrders/merge | Não  | 
| reservationOrders/reservations | Não  | 
| reservationOrders/reservations/revisions | Não  | 
| reservationOrders/return | Não  | 
| reservationOrders/split | Não  | 
| reservationOrders/swap | Não  | 
| reservations | Não  | 
| recursos | Não  | 
| validateReservationOrder | Não  | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| edgenodes | Não  | 
| perfis | Sim | 
| profiles/endpoints | Sim | 
| profiles/endpoints/origins | Não  | 
| profiles/endpoints/origins | Não  | 
| validateProbe | Não  | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| certificateOrders | Sim | 
| certificateOrders/certificates | Não  | 
| validateCertificateRegistrationInformation | Não  | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| recursos | Não  | 
| domainNames | Não  | 
| domainNames/capabilities | Não  | 
| domainNames/internalLoadBalancers | Não  | 
| domainNames/serviceCertificates | Não  | 
| domainNames/slots | Não  | 
| domainNames/slots | Não  | 
| moveSubscriptionResources | Não  | 
| operatingSystemFamilies | Não  | 
| operatingSystems | Não  | 
| quotas | Não  | 
| resourceTypes | Não  | 
| validateSubscriptionMoveAvailability | Não  | 
| virtualMachines | Não  | 
| virtualMachines/diagnosticSettings | Não  | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Migração de infraestrutura clássica | Não  | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| recursos | Não  | 
| expressRouteCrossConnections | Não  | 
| expressRouteCrossConnections/peerings | Não  | 
| gatewaySupportedDevices | Não  | 
| networkSecurityGroups | Não  | 
| quotas | Não  | 
| reservedIps | Não  | 
| virtualNetworks | Não  | 
| virtualNetworks/virtualNetworkPeerings | Não  | 
| virtualNetworks/virtualNetworkPeerings | Não  | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| recursos | Não  | 
| disks | Não  | 
| images | Não  | 
| osImages | Não  | 
| osPlatformImages | Não  | 
| publicImages | Não  | 
| quotas | Não  | 
| storageAccounts | Não  | 
| storageAccounts/services | Não  | 
| storageAccounts/services/diagnosticSettings | Não  | 
| storageAccounts/vmImages | Não  | 
| vmImages | Não  | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| RateCard | Não  | 
| UsageAggregates | Não  | 

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| availabilitySets | Sim | 
| disks | Sim | 
| images | Sim | 
| restorePointCollections | Sim | 
| restorePointCollections/restorePoints | Não  | 
| sharedVMImages | Sim | 
| sharedVMImages/versions | Sim | 
| snapshots | Sim | 
| virtualMachines | Sim | 
| virtualMachines/diagnosticSettings | Não  | 
| virtualMachines/extensions | Sim | 
| virtualMachineScaleSets | Sim | 
| virtualMachines/extensions | Não  | 
| virtualMachineScaleSets/networkInterfaces | Não  | 
| virtualMachineScaleSets/publicIPAddresses | Não  | 
| virtualMachineScaleSets/virtualMachines | Não  | 
| virtualMachineScaleSets/networkInterfaces | Não  | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| AggregatedCost | Não  | 
| Saldos | Não  | 
| Orçamentos | Não  | 
| Charges | Não  | 
| CostTags | Não  | 
| credits | Não  | 
| events | Não  | 
| Previsões | Não  | 
| lots | Não  | 
| Marketplaces | Não  | 
| Pricesheets | Não  | 
| products | Não  | 
| ReservationDetails | Não  | 
| ReservationRecommendations | Não  | 
| ReservationSummaries | Não  | 
| ReservationTransactions | Não  | 
| Marcas | Não  | 
| Termos | Não  | 
| UsageDetails | Não  | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| containerGroups | Sim | 
| serviceAssociationLinks | Não  | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| registries | Sim | 
| registries/builds | Não  | 
| registries/builds/cancel | Não  | 
| registries/builds/getLogLink | Não  | 
| registries/buildTasks | Sim | 
| registries/buildTasks/steps | Não  | 
| registries/eventGridFilters | Não  | 
| registries/getBuildSourceUploadUrl | Não  | 
| registries/GetCredentials | Não  | 
| registries/importImage | Não  | 
| registries/queueBuild | Não  | 
| registries/regenerateCredential | Não  | 
| registries/regenerateCredentials | Não  | 
| registries/replications | Sim | 
| registries/runs | Não  | 
| registries/runs/cancel | Não  | 
| registries/scheduleRun | Não  | 
| registries/tasks | Sim | 
| registries/updatePolicies | Não  | 
| registries/webhooks | Sim | 
| registries/webhooks/getCallbackConfig | Não  | 
| registries/webhooks/ping | Não  | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| containerServices | Sim | 
| managedClusters | Sim | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| de dimensionamento da Web | Sim | 
| updateCommunicationPreference | Não  | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Alertas | Não  | 
| BillingAccounts | Não  | 
| Conectores | Sim | 
| Departments | Não  | 
| Dimensões | Não  | 
| EnrollmentAccounts | Não  | 
| Consultar | Não  | 
| register | Não  | 
| Reportconfigs | Não  | 
| Relatórios | Não  | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| hubs | Sim | 
| hubs/authorizationPolicies | Não  | 
| hubs/connectors | Não  | 
| hubs/connectors/mappings | Não  | 
| hubs/interactions | Não  | 
| hubs/kpi | Não  | 
| hubs/links | Não  | 
| hubs/profiles | Não  | 
| hubs/roleAssignments | Não  | 
| hubs/roles | Não  | 
| hubs/suggestTypeSchema | Não  | 
| hubs/views | Não  | 
| hubs/widgetTypes | Não  | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| jobs | Sim | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| DataBoxEdgeDevices | Sim | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| workspaces | Sim | 
| workspaces/virtualNetworkPeerings | Não  | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| catalogs | Sim | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| connectionManagers | Sim | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| dataFactories | Sim | 
| dataFactories/diagnosticSettings | Não  | 
| dataFactorySchema | Não  | 
| factories | Sim | 
| factories/integrationRuntimes | Não  | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | Sim | 
| accounts/dataLakeStoreAccounts | Não  | 
| accounts/storageAccounts | Não  | 
| accounts/storageAccounts/containers | Não  | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | Sim | 
| accounts/eventGridFilters | Não  | 
| accounts/firewallRules | Não  | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| services | Sim | 
| services/projects | Sim | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | Sim | 
| servers/recoverableServers | Não  | 
| servers/virtualNetworkRules | Não  | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | Sim | 
| servers/recoverableServers | Não  | 
| servers/virtualNetworkRules | Não  | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| servers | Sim | 
| servers/advisors | Não  | 
| servers/queryTexts | Não  | 
| servers/recoverableServers | Não  | 
| servers/topQueryStatistics | Não  | 
| servers/virtualNetworkRules | Não  | 
| servers/waitStatistics | Não  | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| IotHubs | Sim | 
| IotHubs/eventGridFilters | Não  | 
| ProvisioningServices | Sim | 
| usages | Não  | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| controladores | Sim | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| labs | Sim | 
| labs/serviceRunners | Sim | 
| labs/virtualMachines | Sim | 
| schedules | Sim | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| databaseAccounts | Não  | 
| databaseAccounts | Sim | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| domains | Sim | 
| domains/domainOwnershipIdentifiers | Não  | 
| generateSsoRequest | Não  | 
| topLevelDomains | Não  | 
| validateDomainRegistrationInformation | Não  | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| lcsprojects | Não  | 
| lcsprojects/clouddeployments | Não  | 
| lcsprojects/connectors | Não  | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| domains | Sim | 
| domains/topics | Não  | 
| eventSubscriptions | Não  | 
| extensionTopics | Não  | 
| topics | Sim | 
| topicTypes | Não  | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| clusters | Sim | 
| namespaces | Sim | 
| namespaces/authorizationrules | Não  | 
| namespaces/disasterrecoveryconfigs | Não  | 
| namespaces/eventhubs | Não  | 
| namespaces/eventhubs/authorizationrules | Não  | 
| namespaces/eventhubs/consumergroups | Não  | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| recursos | Não  | 
| providers | Não  | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| enroll | Não  | 
| galleryitems | Não  | 
| generateartifactaccessuri | Não  | 
| myareas | Não  | 
| myareas/areas | Não  | 
| myareas/areas/areas | Não  | 
| myareas/areas/areas/galleryitems | Não  | 
| myareas/areas/galleryitems | Não  | 
| myareas/galleryitems | Não  | 
| register | Não  | 
| recursos | Não  | 
| retrieveresourcesbyid | Não  | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| guestConfigurationAssignments | Não  | 
| software | Não  | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| hanaInstances | Sim | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| clusters | Sim | 
| clusters/applications | Não  | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| jobs | Sim | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| labelGroups | Não  | 
| labelGroups/labels | Não  | 
| labelGroups/labels/conditions | Não  | 
| labelGroups/labels/subLabels | Não  | 
| labelGroups/labels/subLabels/conditions | Não  | 

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| actiongroups | Sim | 
| activityLogAlerts | Sim | 
| alertrules | Sim | 
| automatedExportSettings | Não  | 
| autoscalesettings | Sim | 
| baseline | Não  | 
| calculatebaseline | Não  | 
| components | Sim | 
| components/metrics | Não  | 
| components/pricingPlans | Não  | 
| components/query | Não  | 
| diagnosticSettings | Não  | 
| diagnosticSettingsCategories | Não  | 
| eventCategories | Não  | 
| eventtypes | Não  | 
| extendedDiagnosticSettings | Não  | 
| logDefinitions | Não  | 
| logprofiles | Não  | 
| logs | Não  | 
| metricAlerts | Sim |
| migrateToNewPricingModel | Não  | 
| myWorkbooks | Não  | 
| consultas | Não  | 
| rollbackToLegacyPricingModel | Não  | 
| scheduledqueryrules | Sim | 
| vmInsightsOnboardingStatuses | Não  | 
| webtests | Sim | 
| workbooks | Sim | 

## <a name="microsoftintune"></a>Microsoft.Intune
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| diagnosticSettings | Não  | 
| diagnosticSettingsCategories | Não  | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| IoTApps | Sim | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Grafo | Sim | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| deletedVaults | Não  | 
| vaults | Sim | 
| vaults/accessPolicies | Não  | 
| vaults/secrets | Não  | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| clusters | Sim | 
| clusters/databases | Não  | 
| clusters/databases/dataconnections | Não  | 
| clusters/databases/eventhubconnections | Não  | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| labaccounts | Sim | 
| users | Não  | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| logs | Não  | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| integrationAccounts | Sim | 
| workflows | Sim | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| commitmentPlans | Sim | 
| webServices | Sim | 
| Workspaces | Sim | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | Sim | 
| accounts/workspaces | Sim | 
| accounts/workspaces/projects | Sim | 
| teamAccounts | Sim | 
| teamAccounts/workspaces | Sim | 
| teamAccounts/workspaces/projects | Sim | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| workspaces | Sim | 
| workspaces/computes | Não  | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Identidades | Não  | 
| userAssignedIdentities | Sim | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| getEntities | Não  | 
| managementGroups | Não  | 
| recursos | Não  | 
| startTenantBackfill | Não  | 
| tenantBackfillStatus | Não  | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | Sim | 
| accounts/eventGridFilters | Não  | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| offers | Não  | 
| offerTypes | Não  | 
| offerTypes/publishers | Não  | 
| offerTypes/publishers/offers | Não  | 
| offerTypes/publishers/offers/plans | Não  | 
| offerTypes/publishers/offers/plans/agreements | Não  | 
| offerTypes/publishers/offers/plans/configs | Não  | 
| offerTypes/publishers/offers/plans/configs/importImage | Não  | 
| privategalleryitems | Não  | 
| products | Não  | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| classicDevServices | Sim | 
| updateCommunicationPreference | Não  | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| Contratos | Não  | 
| offertypes | Não  | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| mediaservices | Sim | 
| mediaservices/accountFilters | Não  | 
| mediaservices/assets | Não  | 
| mediaservices/assets/assetFilters | Não  | 
| mediaservices/contentKeyPolicies | Não  | 
| mediaservices/eventGridFilters | Não  | 
| mediaservices/liveEvents | Não  | 
| mediaservices/liveEvents | Sim | 
| mediaservices/liveEvents/liveOutputs | Não  | 
| mediaservices/liveEvents | Não  | 
| mediaservices/streamingEndpoints | Não  | 
| mediaservices/streamingEndpoints | Sim | 
| mediaservices/streamingLocators | Não  | 
| mediaservices/streamingLocators | Não  | 
| mediaservices/transforms | Não  | 
| mediaservices/transforms | Não  | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| projects | Sim | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| applicationGateways | Sim | 
| applicationSecurityGroups | Sim | 
| azureFirewallFqdnTags | Não  | 
| azureFirewalls | Sim | 
| bgpServiceCommunities | Não  | 
| connections | Sim | 
| ddosCustomPolicies | Sim | 
| ddosProtectionPlans | Sim | 
| dnsOperationStatuses | Não  | 
| dnszones | Sim | 
| dnszones/A | Não  | 
| dnszones/AAAA | Não  | 
| dnszones/all | Não  | 
| dnszones/CAA | Não  | 
| dnszones/CNAME | Não  | 
| dnszones/MX | Não  | 
| dnszones/NS | Não  | 
| dnszones/PTR | Não  | 
| dnszones/recordsets | Não  | 
| dnszones/SOA | Não  | 
| dnszones/SRV | Não  | 
| dnszones/TXT | Não  | 
| expressRouteCircuits | Sim | 
| expressRouteServiceProviders | Não  | 
| frontdoors | Sim | 
| frontdoorWebApplicationFirewallPolicies | Sim | 
| getDnsResourceReference | Não  | 
| interfaceEndpoints | Sim | 
| internalNotify | Não  | 
| loadBalancers | Sim | 
| virtualNetworkGateways | Sim | 
| natGateways | Sim | 
| networkIntentPolicies | Sim | 
| networkInterfaces | Sim | 
| networkProfiles | Sim | 
| networkSecurityGroups | Sim | 
| networkWatchers | Sim | 
| networkWatchers/connectionMonitors | Sim | 
| networkWatchers/lenses | Sim | 
| networkWatchers/lenses | Sim | 
| privateLinkServices | Sim | 
| publicIPAddresses | Sim | 
| publicIPPrefixes | Sim | 
| routeFilters | Sim | 
| routeTables | Sim | 
| serviceEndpointPolicies | Sim | 
| trafficManagerGeographicHierarchies | Não  | 
| trafficmanagerprofiles | Sim | 
| trafficmanagerprofiles/heatMaps | Não  | 
| virtualWans | Sim | 
| virtualNetworkGateways | Sim | 
| virtualNetworks | Sim | 
| virtualNetworkTaps | Sim | 
| virtualWans | Sim | 
| vpnGateways | Sim | 
| vpnSites | Sim | 
| frontdoorWebApplicationFirewallPolicies | Sim | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| namespaces | Sim | 
| namespaces/notificationHubs | Sim | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| dispositivos | Não  | 
| linkTargets | Não  | 
| storageInsightConfigs | Não  | 
| workspaces | Sim | 
| workspaces/dataSources | Não  | 
| workspaces/linkedServices | Não  | 
| workspaces/query | Não  | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| managementassociations | Não  | 
| managementconfigurations | Sim | 
| solutions | Sim | 
| Modos de exibição | Sim | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| policyEvents | Não  | 
| policyStates | Não  | 
| policyTrackedResources | Não  | 
| remediations | Não  | 

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| consoles | Não  | 
| dashboards | Sim | 
| userSettings | Não  | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| workspaceCollections | Sim | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| capacities | Sim | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| backupProtectedItems | Não  | 
| vaults | Sim | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| namespaces | Sim | 
| namespaces/authorizationrules | Não  | 
| namespaces/hybridconnections | Não  | 
| namespaces/hybridconnections/authorizationrules | Não  | 
| namespaces/wcfrelays | Não  | 
| namespaces/wcfrelays/authorizationrules | Não  | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| recursos | Não  | 
| subscriptionsStatus | Não  | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| availabilityStatuses | Não  | 
| childAvailabilityStatuses | Não  | 
| childResources | Não  | 
| events | Não  | 
| impactedResources | Não  | 
| Notificações | Não  | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| deployments | Não  | 
| deployments/operations | Não  | 
| links | Não  | 
| notifyResourceJobs | Não  | 
| providers | Não  | 
| resourceGroups | Não  | 
| recursos | Não  | 
| subscriptions | Não  | 
| subscriptions/providers | Não  | 
| subscriptions/resourceGroups | Não  | 
| subscriptions/resourcegroups/resources | Não  | 
| subscriptions/resources | Não  | 
| subscriptions/tagnames | Não  | 
| subscriptions/tagNames/tagValues | Não  | 
| tenants | Não  | 

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| de dimensionamento da Web | Sim | 
| saasresources | Não  | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| flows | Sim | 
| jobcollections | Sim | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| resourceHealthMetadata | Não  | 
| searchServices | Sim | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| advancedThreatProtectionSettings | Não  | 
| alertas | Não  | 
| allowedConnections | Não  | 
| appliances | Não  | 
| applicationWhitelistings | Não  | 
| AutoProvisioningSettings | Não  | 
| Compliances | Não  | 
| dataCollectionAgents | Não  | 
| discoveredSecuritySolutions | Não  | 
| externalSecuritySolutions | Não  | 
| InformationProtectionPolicies | Não  | 
| jitNetworkAccessPolicies | Não  | 
| monitoring | Não  | 
| monitoring/antimalware | Não  | 
| monitoring/baseline | Não  | 
| monitoring/patch | Não  | 
| políticas | Não  | 
| pricings | Não  | 
| securityContacts | Não  | 
| securitySolutions | Não  | 
| securitySolutionsReferenceData | Não  | 
| securityStatus | Não  | 
| securityStatus/endpoints | Não  | 
| securityStatus/subnets | Não  | 
| securityStatus/virtualMachines | Não  | 
| securityStatuses | Não  | 
| securityStatusesSummaries | Não  | 
| configurações | Não  | 
| tarefas | Não  | 
| topologies | Não  | 
| workspaceSettings | Não  | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| diagnosticSettings | Não  | 
| diagnosticSettingsCategories | Não  | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| namespaces | Sim | 
| namespaces/authorizationrules | Não  | 
| namespaces/disasterrecoveryconfigs | Não  | 
| namespaces/eventgridfilters | Não  | 
| namespaces/queues | Não  | 
| namespaces/queues/authorizationrules | Não  | 
| namespaces/topics | Não  | 
| namespaces/topics/authorizationrules | Não  | 
| namespaces/topics/subscriptions | Não  | 
| namespaces/topics/subscriptions/rules | Não  | 
| premiumMessagingRegions | Não  | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| clusters | Sim | 
| clusters/applications | Não  | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| de dimensionamento da Web | Sim | 
| gateways | Sim | 
| networks | Sim | 
| segredos | Sim | 
| volumes | Sim | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| SignalR | Sim | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| applianceDefinitions | Sim | 
| appliances | Sim | 
| applicationDefinitions | Sim | 
| de dimensionamento da Web | Sim | 
| jitRequests | Sim | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| managedInstances | Sim |
| managedInstances/databases | Sim (veja a observação a seguir) |
| managedInstances/databases/backupShortTermRetentionPolicies | Não  |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não  |
| managedInstances/databases/vulnerabilityAssessments | Não  |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não  |
| managedInstances/encryptionProtector | Não  |
| managedInstances/keys | Não  |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não  |
| managedInstances/vulnerabilityAssessments | Não  |
| servers | Sim | 
| servers/administrators | Não  | 
| servers/communicationLinks | Não  | 
| servers/databases | Sim (veja a observação a seguir) | 
| servers/encryptionProtector | Não  | 
| servers/keys | Não  | 
| servers/restorableDroppedDatabases | Não  | 
| servers/serviceobjectives | Não  | 
| servers/tdeCertificates | Não  | 

> [!NOTE]
> O banco de dados mestre não oferece suporte a marcas, mas outros bancos de dados dão.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| SqlVirtualMachineGroups | Sim | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Não  | 
| SqlVirtualMachines | Sim | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| storageAccounts | Sim | 
| storageAccounts/blobServices | Não  | 
| storageAccounts/blobServices | Não  | 
| storageAccounts/blobServices | Não  | 
| storageAccounts/services | Não  | 
| storageAccounts/blobServices | Não  | 
| usages | Não  | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| storageSyncServices | Sim | 
| storageSyncServices/registeredServers | Não  | 
| storageSyncServices/syncGroups | Não  | 
| storageSyncServices/syncGroups/cloudEndpoints | Não  | 
| storageSyncServices/syncGroups/cloudEndpoints | Não  | 
| storageSyncServices/workflows | Não  | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| managers | Sim | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| streamingjobs | Sim (veja a observação a seguir) | 
| streamingjobs/diagnosticSettings | Não  | 

> [!NOTE]
> Você não pode adicionar uma marca quando streamingjobs estiver em execução. Pare o recurso para adicionar uma marca.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| CreateSubscription | Não  | 
| SubscriptionDefinitions | Não  | 
| SubscriptionDefinitions | Não  | 

## <a name="microsoftsupport"></a>microsoft.support
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| supporttickets | Não  | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| providerRegistrations | Sim | 
| recursos | Sim | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| environments | Sim | 
| environments/accessPolicies | Não  | 
| environments/eventsources | Sim | 
| environments/referenceDataSets | Sim | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| conta | Sim | 
| account/extension | Sim | 
| account/project | Sim | 

## <a name="microsoftweb"></a>Microsoft.Web
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
| certificates | Sim | 
| connectionGateways | Sim | 
| connections | Sim | 
| customApis | Sim | 
| deletedSites | Não  | 
| funções | Não  | 
| hostingEnvironments | Sim | 
| hostingEnvironments/multiRolePools | Não  | 
| hostingEnvironments/multiRolePools/instances | Não  | 
| hostingEnvironments/multiRolePools | Não  | 
| hostingEnvironments/workerPools/instances | Não  | 
| publishingUsers | Não  | 
| filmes | Não  | 
| resourceHealthMetadata | Não  | 
| runtimes | Não  | 
| serverFarms | Sim | 
| serverFarms/workers | Não  | 
| sites | Sim | 
| sites/domainOwnershipIdentifiers | Não  | 
| sites/hostNameBindings | Não  | 
| sites/instances | Não  | 
| sites/instances/extensions | Não  | 
| sites/premieraddons | Sim | 
| sites/recommendations | Não  | 
| sites/resourceHealthMetadata | Não  | 
| sites/slots | Sim | 
| sites/hostNameBindings | Não  | 
| sites/instances | Não  | 
| sites/instances/extensions | Não  | 
| sourceControls | Não  | 
| validade | Não  | 
| verifyHostingEnvironmentVnet | Não  | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| diagnosticSettings | Não  | 
| diagnosticSettingsCategories | Não  | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| DeviceServices | Sim | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Tipo de recurso | Suporte de Tags |
| ------------- | ----------- |
| components | Não  | 
| componentsSummary | Não  | 
| monitorInstances | Não  | 
| monitorInstancesSummary | Não  | 
| monitors | Não  | 
| notificationSettings | Não  | 

## <a name="next-steps"></a>Próximas etapas
Para saber como aplicar tags a recursos, consulte [Use tags para organizar seus recursos do Azure](resource-group-using-tags.md).
