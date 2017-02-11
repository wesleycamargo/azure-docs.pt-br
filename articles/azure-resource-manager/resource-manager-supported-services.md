---
title: "Serviços com suporte do Resource Manager | Microsoft Docs"
description: "Descreve os provedores de recursos que oferecem suporte ao Gerenciador de Recursos, aos respectivos esquemas e versões de API disponíveis, bem como às regiões que podem hospedar os recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2016
ms.author: magoedte;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: dabe7d9796ab24a257ea904bc5d978cb71d7e149
ms.openlocfilehash: af96c2b6063f7200cdeb6b51e5c729aa4643127f


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Provedores, regiões, versões de API e esquemas do Gerenciador de Recursos
O Gerenciador de Recursos do Azure fornece uma nova maneira de implantar e gerenciar os serviços que compõem seus aplicativos. A maioria (não todos) dos serviços suporta o Gerenciador de Recursos e alguns serviços suportam o Gerenciador de Recursos apenas parcialmente. Este tópico fornece uma lista de provedores de recursos com suporte para o Gerenciador de Recursos do Azure.

Ao implantar seus recursos, você também precisa saber quais são as regiões que oferecem suporte a esses recursos e quais versões de API estão disponíveis para os recursos. A seção [Regiões com suporte](#supported-regions) mostra como descobrir quais regiões funcionam para sua assinatura e seus recursos. A seção [Versões de API com suporte](#supported-api-versions) mostra como determinar quais versões de API você pode usar.

Para ver quais serviços têm suporte no Portal do Azure e no portal clássico, confira o [Gráfico de disponibilidade do Portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/). Para ver quais serviços oferecem suporte a recursos de movimentação, confira [Mover recursos para o novo grupo de recursos ou assinatura](resource-group-move-resources.md).

As tabelas a seguir listam quais serviços Microsoft dão suporte à implantação e o gerenciamento por meio do Resource Manager e quais não. O link na coluna **Modelos de Início Rápido** envia uma consulta ao repositório Modelos de Início Rápido do Azure para o provedor de recursos especificado. Os modelos de início rápido são adicionados e atualizados com frequência. A presença de um link para um serviço específico não significa necessariamente que a consulta retorna modelos do repositório. Também há muitos provedores de recursos de terceiros que dão suporte ao Resource Manager. Saiba como ver todos os provedores de recursos na seção [Provedores de recursos e tipos](#resource-providers-and-types).

## <a name="compute"></a>Computação
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- |
| Batch |Sim |[REST do Lote](/rest/api/batchservice) |[Esquema do Lote](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json) | |
| Contêiner |Sim |[REST do Serviço de Contêiner](/rest/api/containerregistry) |[Esquema do Contêiner](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) |[Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Serviços de ciclo de vida do Dynamics |Sim | | | |
| Conjuntos de Dimensionamento |Sim |[REST do Conjunto de Dimensionamento](/rest/api/compute/virtualmachinescalesets) |[Esquema do Conjunto de Dimensionamento](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) |
| Service Fabric |Sim |[Service Fabric Rest](/rest/api/servicefabric) | [Esquema do Service Fabric](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-09-01/Microsoft.ServiceFabric.json) |[Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Máquinas Virtuais |Sim |[VM REST](/rest/api/compute/virtualmachines) |[Esquema da VM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |[virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Máquinas virtuais (clássico) |Limitado |- |- |- |
| Aplicativo Remoto |Não |- |- |- |
| Serviços de Nuvem (clássico) |Limitado (veja abaixo) |- |- |- |

Máquinas virtuais (clássico) refere-se aos recursos que foram implantados por meio do modelo de implantação clássico, não por meio do modelo de implantação do Gerenciador de Recursos. Em geral, esses recursos não oferecem suporte a operações do Gerenciador de Recursos, mas existem algumas operações que foram habilitadas. Para saber mais sobre esses modelos de implantação, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](resource-manager-deployment-model.md). 

Os Serviços de Nuvem (clássicos) podem ser usados com outros recursos clássicos. No entanto, os recursos clássicos não aproveitarão todos os recursos do Resource Manager e não são uma boa opção para soluções futuras. Em vez disso, considere alterar a infraestrutura de aplicativos para usar os recursos dos namespaces Microsoft.Compute, Microsoft.Storage e Microsoft.Network.

## <a name="networking"></a>Rede
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- |
| Application Gateway |Sim |[REST do Application Gateway](https://msdn.microsoft.com/library/azure/mt684939.aspx) | |[applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS |Sim |[DNS REST](/rest/api/dns) |[Esquema do DNS](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) |[dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| Rota Expressa |Sim |[REST da Rota Expressa](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |[expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Balanceador de carga |Sim |[REST do balanceador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Esquema do Balanceador de Carga](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Gerenciador de Tráfego |Sim |[REST do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Esquema do Gerenciador de Tráfego](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) |[trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Redes Virtuais |Sim |[REST da Rede Virtual](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) |[Esquema da Rede Virtual](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |[virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Gateway de VPN |Sim |[REST de Gateway de rede](https://msdn.microsoft.com/library/azure/mt163859.aspx) | |[virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |

## <a name="storage"></a>Armazenamento
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- | --- |
| Armazenamento |Sim |[REST de armazenamento](/rest/api/storagerp) |[Conta de armazenamento](resource-manager-template-storage.md) |[Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple |Sim | | | |

## <a name="databases"></a>Bancos de dados
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- | --- |
| Banco de Dados de Documentos |Sim |[REST do Banco de Dados de Documentos](/rest/api/documentdbresourceprovider) |[Esquema do DocumentDB](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) |[Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Cache Redis |Sim | [REST do Cache Redis](/rest/api/redis) |[Esquema do Redis](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) |[Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Banco de Dados SQL |Sim |[REST do Banco de Dados SQL](/rest/api/sql) |[Esquema do Banco de Dados SQL](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |[Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL Data Warehouse |Sim | | | |

## <a name="web--mobile"></a>Web e serviços móveis
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- |
| Aplicativos de API |Sim | [REST do Serviço de Aplicativo](/rest/api/appservice) |[Esquema dos Aplicativos de API](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Aplicativos de API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Gerenciamento da API |Sim |[REST de gerenciamento de API](/rest/api/apimanagement) |[Esquema do Gerenciamento de API](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json) |[Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) |
| Content Moderator |Sim | | | |
| Aplicativo de Funções |Sim | [REST do Aplicativo de Funções](/rest/api/appservice) | |[functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Aplicativos Lógicos |Sim |[REST dos Aplicativos Lógicos](/rest/api/logic) |[Esquema dos Aplicativos Lógicos](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) |[Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Aplicativos Móveis |Sim | [REST do Serviço de Aplicativo](/rest/api/appservice) |[Esquema dos Aplicativos Móveis](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code) |
| Compromisso de mobilidade |Sim |[REST do Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |[Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Pesquisar |Sim |[REST de pesquisa](/rest/api/searchservice) | [Esquema do Search](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-19/Microsoft.Search.json) |[Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Aplicativos Web |Sim | [REST dos Aplicativos Web](/rest/api/appservice/webapps) |[Esquema dos Aplicativos Web](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |[Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="intelligence--analytics"></a>Inteligência + Análises
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- |
| Serviços de análise | Sim | [REST do Analysis Services](/rest/api/analysisservices) | [Esquema do Analysis Services](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-16/Microsoft.AnalysisServices.json) | |
| Serviços Cognitivos |Sim | [REST dos Serviços Cognitivos](/rest/api/cognitiveservices) |[Esquema dos Serviços Cognitivos](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) | |
| Catálogo de Dados |Sim |[REST do Catálogo de Dados](/rest/api/datacatalog) |[Esquema do Catálogo de Dados](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) | |
| Data Factory |Sim |[REST do Data Factory](/rest/api/datafactory) | |[Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Análises Data Lake |Sim | [REST do Data Lake](/rest/api/datalakeanalytics) |[Esquema do Data Lake](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Repositório Data Lake |Sim |[REST do Data Lake Store](/rest/api/datalakestore) |[Esquema do Data Lake](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) |[Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights |Sim |[REST do HDInsights](/rest/api/hdinsight) | |[Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Aprendizado de Máquina |Sim |[REST do Aprendizado de Máquina](/rest/api/machinelearning) |[Esquema do Machine Learning](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) | |
| Stream Analytics |Sim |[Análise de fluxo REST](/rest/api/streamanalytics) | | |
| Power BI |Sim |[REST do Power BI Embedded](/rest/api/powerbiembedded) |[Esquema do Power BI](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) | |


## <a name="internet-of-things"></a>Internet das coisas
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- |
| Hub de evento |Sim |[REST do Hub de Eventos](/rest/api/eventhub) |[Esquema do Hub de Eventos](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) |[Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| IoTHubs |Sim |[REST do Hub IoT](/rest/api/iothub) |[Esquema do Hub IoT](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) |[Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Hubs de Notificação |Sim |[REST do Hub de notificação](/rest/api/notificationhubs) |[Esquema do Hub de Notificação](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |[Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Mídia e CDN
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- |
| CDN |Sim |[REST CDN](/rest/api/cdn) |[Esquema da CDN](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) |[Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Serviço de mídia |Sim |[REST dos Serviços de Mídia](/rest/api/media) |[Esquema dos Serviços de Mídia](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) | |

## <a name="hybrid-integration"></a>Integração híbrida
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- |
| Serviços do BizTalk |Sim | |[Esquema do BizTalk](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | |
| Serviço de Recuperação |Sim |[REST dos Serviços de Recuperação](/rest/api/recoveryservices) |[Esquema dos Serviços de Recuperação](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) |[Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Barramento de Serviço |Sim |[REST do Barramento de Serviço](/rest/api/servicebus) |[Esquema do Barramento de Serviço](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json) |[Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Gerenciamento de acesso e identidade
O Active Directory do Azure funciona com o Gerenciador de Recursos para habilitar o controle de acesso baseado em função para sua assinatura. Para saber mais sobre como usar o controle de acesso baseado em funções e o Active Directory, consulte [Controle de Acesso Baseado em Funções do Azure](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Serviços para Desenvolvedores
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- |
| Monitoramento |Sim |[REST do Monitor](/rest/api/monitor) |[Esquema do Insights](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |[Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Mapas |Sim | | | |
| Laboratórios de Desenvolvimento/Teste |Sim | [REST de Desenvolvimento/Teste](/rest/api/dtl) |[Esquema do Dev/Test Lab](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) |[Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Conta do Visual Studio |Sim | |[Esquema do Visual Studio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | |

## <a name="management-and-security"></a>Gerenciamento e segurança
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- |
| Automação |Sim |[Automação REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Esquema da Automação](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json) |[Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Cofre da Chave |Sim |[REST do Cofre da Chave](/rest/api/keyvault) |[Key vault](resource-manager-template-keyvault.md)<br />[Segredo do cofre da chave](resource-manager-template-keyvault-secret.md) |[Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Insights Operacionais |Sim | | | |
| Agendador |Sim |[REST do Agendador](/rest/api/scheduler) |[Esquema do Agendador](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) |[Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Segurança (visualização) |Sim |[REST de Segurança](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |[Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## <a name="resource-manager"></a>Gerenciador de Recursos
| Recurso | Gerenciador de Recursos habilitado | API REST | Esquema | Modelos de Início Rápido |
| --- | --- | --- | --- | --- | --- |
| Autorização |Sim |[REST da Autorização](/rest/api/authorization) |[Bloqueio de recurso](resource-manager-template-lock.md)<br />[Atribuições de função](resource-manager-template-role.md) |[Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Recursos |Sim |[REST dos Recursos](/rest/api/resources) |[Link de recursos](resource-manager-template-links.md) |[Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |

## <a name="resource-providers-and-types"></a>Provedores e tipos de recursos
Ao implantar recursos, com frequência você precisa recuperar informações sobre os provedores e tipos de recursos. Você pode recuperar essas informações por meio da API REST, do Azure PowerShell ou da CLI do Azure.

Para trabalhar com um provedor de recursos, o provedor de recursos deve ser registrado com sua conta. Por padrão, vários provedores de recursos são automaticamente registrados; no entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. Os exemplos abaixo mostram como obter o status do registro de um provedor de recursos e registrar o provedor de recursos, se necessário.

### <a name="portal"></a>Portal
Veja uma lista dos provedores de recursos com suporte selecionando **Provedores de recursos** na folha da assinatura. Para registrar sua assinatura em um provedor de recursos, selecione o link **Registrar**.
   
![listar provedores de recursos](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>API REST
Para obter todos os provedores de recursos disponíveis, incluindo seus tipos, suas localizações, suas versões de API e seu status do registro, use a operação [List all resource providers](https://docs.microsoft.com/rest/api/resources/providers#Providers_List). Se você precisar registrar um provedor de recursos, consulte [Registrar uma assinatura em um provedor de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register).

### <a name="powershell"></a>PowerShell
O exemplo a seguir mostra como obter todos os provedores de recursos disponíveis.

    Get-AzureRmResourceProvider -ListAvailable


O exemplo a seguir mostra como obter os tipos de recursos para um provedor de recursos específico.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes

A saída deverá ser semelhante a:

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...

Para registrar um provedor de recursos, forneça o namespace:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>CLI do Azure
O exemplo a seguir mostra como obter todos os provedores de recursos disponíveis.

    azure provider list

A saída deverá ser semelhante a:

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Salve as informações de um provedor de recursos específico em um arquivo com o seguinte comando:

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Para registrar um provedor de recursos, forneça o namespace:

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Regiões com suporte
Durante a implantação de recursos, você normalmente precisa especificar uma região para os recursos. O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações em sua assinatura que impedem o uso de algumas regiões que oferecem suporte aos recursos. Essas limitações podem estar relacionadas a problemas tributários de seu país de residência ou ao resultado de uma política colocada pelo seu administrador de assinatura para usar somente determinadas regiões. 

Para obter uma lista completa de todas as regiões com suporte em todos os serviços do Azure, consulte [Serviços por região](https://azure.microsoft.com/regions/#services). No entanto, essa lista pode incluir regiões sem suporte em sua assinatura. É possível determinar as regiões para um determinado tipo de recurso ao qual sua assinatura dá suporte por meio do Portal, API REST, PowerShell ou CLI do Azure.

### <a name="portal"></a>Portal
Por meio das seguintes etapas, você pode ver as regiões com suporte para um tipo de recurso:

1. Selecione **Mais serviços** > **Gerenciador de Recursos**.
   
    ![Gerenciador de Recursos](./media/resource-manager-supported-services/select-resource-explorer.png)
2. Abra o nó **Provedores**.
   
    ![selecionar provedores](./media/resource-manager-supported-services/select-providers.png)
3. Selecione um provedor de recursos e exiba as regiões e versões de API com suporte.
   
    ![exibir provedor](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>API REST
Para descobrir quais regiões estão disponíveis para um tipo de recurso específico em sua assinatura, use a operação [Listar todos os fornecedores de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
O exemplo a seguir mostra como obter as regiões com suporte para sites da web.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

A saída deverá ser semelhante a:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>CLI do Azure
O exemplo a seguir retorna todas as localizações com suporte para cada tipo de recurso.

    azure location list

Também é possível filtrar os resultados do local com um utilitário JSON, como o [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Que retorna:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Versões de API com suporte
Ao implantar um modelo, você deve especificar uma versão de API a ser usada para criar cada recurso. A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST. Portanto, a versão da API especificada em seu modelo afetará quais propriedades estarão disponíveis para você durante a criação do modelo. Em geral, você deve selecionar a versão mais recente da API ao criar novos modelos. Para os modelos existentes, você pode decidir se deseja continuar usando uma versão de API anterior ou atualizar o modelo para a versão mais recente para aproveitar os novos recursos.

### <a name="portal"></a>Portal
Você pode determinar as versões da API com suporte da mesma maneira que determinou as regiões com suporte (mostrado anteriormente).

### <a name="rest-api"></a>API REST
Para descobrir quais versões de API está disponíveis para os tipos de recurso, use a operação [Listar todos os provedores de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
O exemplo a seguir mostra como obter as versões de API disponíveis para um tipo de recurso específico.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

A saída deverá ser semelhante a:

    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>CLI do Azure
Salve as informações (incluindo as versões de API disponíveis) de um provedor de recursos em um arquivo com o seguinte comando:

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Você pode abrir o arquivo e localizar o elemento **apiVersions**

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a criação de modelos do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
* Para saber mais sobre como implantar recursos, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).




<!--HONumber=Dec16_HO2-->


