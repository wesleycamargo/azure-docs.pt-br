---
title: Suporte à operação de movimentação pelo tipo de recurso do Azure | Microsoft Docs
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou assinatura.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 5bb820d816115dccf470b6c32d080862495e8310
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434969"
---
# <a name="move-operation-support-for-resources"></a>Mover o suporte de operação para recursos

Este artigo lista se um tipo de recurso do Azure é compatível com a operação de movimentação. Embora um tipo de recurso seja compatível com a operação de movimentação, pode haver condições que impeçam que o recurso seja movido. Para obter detalhes sobre as condições que afetam as operações de movimentação, confira [Move resources to new resource group or subscription](resource-group-move-resources.md) (Mover recursos para um novo grupo de recursos ou assinatura).

## <a name="find-resource-provider-and-resource-type"></a>Localizar o provedor de recursos e o tipo de recurso

Para determinar se um recurso pode ser movido, é necessário localizar o seu provedor de recursos e o tipo de recurso.

Para o PowerShell, use:

```azurepowershell-interactive
Get-AzureRmResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

Para a CLI do Azure, use:

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, reourcetype:type}'
```

O tipo de recurso é retornado no formato `<resource-provider>/<resource-type-name>`. Portanto, o valor `Microsoft.OperationalInsights/workspaces` tem um provedor de recursos de **Microsoft.OperationalInsights** e o nome do tipo de recurso de **workspaces**.

Depois de localizar o provedor de recursos e o tipo de recurso, use as tabelas neste artigo para determinar se o tipo de recurso é compatível com a operação de movimentação.

## <a name="microsoftaad"></a>Microsoft.AAD

| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | --------------- | ----------- |
| domainservices | Não  | Não  |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| servers | SIM | SIM |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | --------------- | ----------- |
| propriedade serviço | SIM | SIM |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | --------------- | ----------- |
| policyassignments | Não  | Não  |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| automationaccounts | SIM | SIM |
| automationaccounts/configurations | SIM | SIM |
| automationaccounts/runbooks | SIM | SIM |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| b2cdirectories | SIM | SIM |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| registrations | SIM | SIM |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| backupvault | Não  | Não  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| batchaccounts | SIM | SIM |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| clusters | Não  | Não  |
| fileservers | Não  | Não  |
| jobs | Não  | Não  |
| espaços de trabalho | Não  | Não  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| mapapis | Não  | Não  |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| biztalk | SIM | SIM |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| blueprintassignments | Não  | Não  |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| botservices | SIM | SIM |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| redis | SIM | SIM |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| perfis | SIM | SIM |
| profiles/endpoints | SIM | SIM |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| certificateorders | SIM | SIM |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| domainnames | SIM | Não  |
| virtualmachines | SIM | Não  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| networksecuritygroups | Não  | Não  |
| reservedips | Não  | Não  |
| virtualnetworks | Não  | Não  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| storageaccounts | SIM | Não  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| accounts | SIM | SIM |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| availabilitysets | SIM | SIM |
| disks | SIM | SIM |
| galleries | Não  | Não  |
| galleries/images | Não  | Não  |
| galleries/images/versions | Não  | Não  |
| images | SIM | SIM |
| restorepointcollections | Não  | Não  |
| sharedvmimages | Não  | Não  |
| sharedvmimages/versions | Não  | Não  |
| snapshots | SIM | SIM |
| virtualmachines | SIM | SIM |
| virtualmachines/extensions | SIM | SIM |
| virtualmachinescalesets | SIM | SIM |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| containergroups | Não  | Não  |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| containergroups | Não  | Não  |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| registries | SIM | SIM |
| registries/buildtasks | SIM | SIM |
| registries/replications | Não  | Não  |
| registries/tasks | SIM | SIM |
| registries/webhooks | SIM | SIM |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| containerservices | Não  | Não  |
| managedclusters | Não  | Não  |
| openshiftmanagedclusters | Não  | Não  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| de dimensionamento da Web | SIM | SIM |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| conectores | SIM | SIM |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| hubs | SIM | SIM |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| jobs | Não  | Não  |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| databoxedgedevices | Não  | Não  |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| espaços de trabalho | Não  | Não  |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| catalogs | SIM | SIM |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| datafactories | SIM | SIM |
| factories | SIM | SIM |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| datalakeaccounts | Não  | Não  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| accounts | SIM | SIM |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| accounts | SIM | SIM |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| services | Não  | Não  |
| services/projects | Não  | Não  |
| slots | Não  | Não  |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| servers | Não  | Não  |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| servers | SIM | SIM |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| servergroups | Não  | Não  |
| servers | SIM | SIM |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| artifactsources | Não  | Não  |
| rollouts | Não  | Não  |
| servicetopologies | Não  | Não  |
| servicetopologies/services | Não  | Não  |
| servicetopologies/services/serviceunits | Não  | Não  |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| iothubs | SIM | SIM |
| provisioningservices | SIM | SIM |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| labcenters | Não  | Não  |
| labs | SIM | Não  |
| labs/servicerunners | SIM | SIM |
| labs/virtualmachines | SIM | Não  |
| schedules | Não  | Não  |

## <a name="microsoftdns"></a>microsoft.dns
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| dnszones | Não  | Não  |
| dnszones/a | Não  | Não  |
| dnszones/aaaa | Não  | Não  |
| dnszones/cname | Não  | Não  |
| dnszones/mx | Não  | Não  |
| dnszones/ptr | Não  | Não  |
| dnszones/srv | Não  | Não  |
| dnszones/txt | Não  | Não  |
| trafficmanagerprofiles | Não  | Não  |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| databaseaccounts | SIM | SIM |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| domains | SIM | SIM |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| topics | SIM | SIM |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| clusters | SIM | SIM |
| namespaces | SIM | SIM |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| hanainstances | SIM | SIM |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| dedicatedhsms | Não  | Não  |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| clusters | SIM | SIM |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| jobs | SIM | SIM |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| actiongroups | SIM | SIM |
| activitylogalerts | Não  | Não  |
| alertrules | SIM | SIM |
| autoscalesettings | SIM | SIM |
| components | SIM | SIM |
| metricalerts | Não  | Não  |
| scheduledqueryrules | SIM | SIM |
| webtests | SIM | SIM |
| workbooks | SIM | SIM |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| iotapps | SIM | SIM |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| vaults | SIM | SIM |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| labaccounts | SIM | SIM |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| accounts | SIM | SIM |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| accounts | SIM | SIM |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| integrationaccounts | SIM | SIM |
| workflows | SIM | SIM |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| commitmentplans | SIM | SIM |
| webservices | SIM | Não  |
| espaços de trabalho | SIM | SIM |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| operationalizationclusters | SIM | SIM |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| accounts | SIM | SIM |
| accounts/workspaces | SIM | SIM |
| accounts/workspaces/projects | SIM | SIM |
| teamaccounts | SIM | SIM |
| teamaccounts/workspaces | SIM | SIM |
| teamaccounts/workspaces/projects | SIM | SIM |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| accounts | SIM | SIM |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| espaços de trabalho | SIM | SIM |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| userassignedidentities | SIM | SIM |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| accounts | SIM | SIM |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| classicdevservices | Não  | Não  |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| mediaservices | SIM | SIM |
| mediaservices/liveevents | SIM | SIM |
| mediaservices/streamingendpoints | SIM | SIM |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| projects | Não  | Não  |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| applicationgateways | Não  | Não  |
| applicationsecuritygroups | SIM | SIM |
| azurefirewalls | SIM | SIM |
| connections | SIM | SIM |
| ddosprotectionplans | Não  | Não  |
| dnszones | SIM | SIM |
| expressroutecircuits | Não  | Não  |
| expressroutecrossconnections | Não  | Não  |
| expressroutegateways | Não  | Não  |
| expressrouteports | Não  | Não  |
| frontdoors | SIM | SIM |
| frontdoorwebapplicationfirewallpolicies | SIM | SIM |
| interfaceendpoints | Não  | Não  |
| loadbalancers | SIM | SIM |
| localnetworkgateways | SIM | SIM |
| networkintentpolicies | SIM | SIM |
| networkinterfaces | SIM | SIM |
| networkprofiles | Não  | Não  |
| networksecuritygroups | SIM | SIM |
| networkwatchers | SIM | SIM |
| networkwatchers/connectionmonitors | SIM | SIM |
| networkwatchers/lenses | SIM | SIM |
| networkwatchers/pingmeshes | SIM | SIM |
| publicipaddresses | SIM | SIM |
| publicipprefixes | SIM | SIM |
| routefilters | Não  | Não  |
| routetables | SIM | SIM |
| serviceendpointpolicies | SIM | SIM |
| trafficmanagerprofiles | SIM | SIM |
| virtualhubs | SIM | SIM |
| virtualnetworkgateways | SIM | SIM |
| virtualnetworks | SIM | SIM |
| virtualnetworktaps | Não  | Não  |
| virtualwans | SIM | SIM |
| vpngateways | SIM | SIM |
| vpnsites | SIM | SIM |
| webapplicationfirewallpolicies | SIM | SIM |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| namespaces | SIM | SIM |
| namespaces/notificationhubs | SIM | SIM |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| espaços de trabalho | SIM | SIM |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| managementconfigurations | SIM | SIM |
| solutions | SIM | SIM |
| Modos de exibição | SIM | SIM |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| dashboards | SIM | SIM |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| workspacecollections | SIM | SIM |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| capacities | SIM | SIM |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| vaults | SIM | SIM |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| namespaces | SIM | SIM |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| de dimensionamento da Web | SIM | Não  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| flows | SIM | SIM |
| jobcollections | SIM | SIM |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| searchservices | SIM | SIM |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| namespaces | SIM | SIM |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| clusters | SIM | SIM |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| de dimensionamento da Web | SIM | SIM |
| networks | SIM | SIM |
| volumes | SIM | SIM |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| signalr | SIM | SIM |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| siterecoveryvault | Não  | Não  |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| appliancedefinitions | Não  | Não  |
| appliances | Não  | Não  |
| applicationdefinitions | Não  | Não  |
| de dimensionamento da Web | Não  | Não  |

## <a name="microsoftsql"></a>Microsoft.Sql
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| managedinstances | SIM | SIM |
| managedinstances/databases | SIM | SIM |
| servers | SIM | SIM |
| servers/databases | SIM | SIM |
| servidores/elasticpools | SIM | SIM |
| virtualclusters | SIM | SIM |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| storageaccounts | SIM | SIM |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| storagesyncservices | SIM | SIM |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| managers | Não  | Não  |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| streamingjobs | SIM | SIM |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| environments | SIM | SIM |
| environments/eventsources | SIM | SIM |
| environments/referencedatasets | SIM | SIM |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| conta | SIM | SIM |
| account/extension | SIM | SIM |
| account/project | SIM | SIM |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| certificates | Não  | SIM |
| classicmobileservices | Não  | Não  |
| connectiongateways | SIM | SIM |
| connections | SIM | SIM |
| customapis | SIM | SIM |
| hostingenvironments | Não  | Não  |
| serverfarms | SIM | SIM |
| sites | SIM | SIM |
| sites/premieraddons | SIM | SIM |
| sites/slots | SIM | SIM |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | -------------- | ------------ |
| deviceservices | SIM | SIM |


## <a name="third-party-services"></a>Serviços de terceiros

No momento, serviços de terceiros não dão suporte à operação de movimentação. Esses provedores de recursos são:

* 84codes.CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.Cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>Próximas etapas

* Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](resource-group-move-resources.md) (Mover recursos para o novo grupo de recursos ou assinatura).
