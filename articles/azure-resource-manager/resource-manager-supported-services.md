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
ms.date: 03/20/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 70a598c9a5bf514f62baa0c5275b2e44cd9776b8
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Provedores, regiões, versões de API e esquemas do Gerenciador de Recursos
Este tópico fornece uma lista de provedores de recursos que dão suporte ao Azure Resource Manager.

Ao implantar seus recursos, você também precisa saber quais são as regiões que oferecem suporte a esses recursos e quais versões de API estão disponíveis para os recursos. A seção [Regiões com suporte](#supported-regions) mostra como descobrir quais regiões funcionam para sua assinatura e seus recursos. A seção [Versões de API com suporte](#supported-api-versions) mostra como determinar quais versões de API você pode usar.

Para ver quais serviços têm suporte no Portal do Azure e no portal clássico, confira o [Gráfico de disponibilidade do Portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/). Para ver quais serviços oferecem suporte a recursos de movimentação, confira [Mover recursos para o novo grupo de recursos ou assinatura](resource-group-move-resources.md).

As tabelas a seguir listam quais serviços Microsoft dão suporte à implantação e o gerenciamento por meio do Resource Manager e quais não. Também há muitos provedores de recursos de terceiros que dão suporte ao Resource Manager. Saiba como ver todos os provedores de recursos na seção [Provedores de recursos e tipos](#resource-providers-and-types).

## <a name="compute"></a>Computação
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- |
| Batch |Sim |[REST do Lote](/rest/api/batchservice) |[Recursos do Lote](/azure/templates/microsoft.batch/batchaccounts) |
| Registro de Contêiner |Sim |[REST de Registro de Contêiner](/rest/api/containerregistry) |[Recursos do Registro de Contêiner](/azure/templates/microsoft.containerregistry/registries) |
| Serviço de Contêiner |Sim |[REST do Serviço de Contêiner](/rest/api/compute/containerservices) |[Recursos do Serviço de Contêiner](/azure/templates/microsoft.containerservice/containerservices) |
| Serviços de ciclo de vida do Dynamics |Sim | | |
| Conjuntos de Dimensionamento |Sim |[REST do Conjunto de Dimensionamento](/rest/api/virtualmachinescalesets/) |[Recursos do Conjunto de Dimensionamento](/azure/templates/microsoft.compute/virtualmachinescalesets) |
| Service Fabric |Sim |[Service Fabric Rest](/rest/api/servicefabric) | [Recursos do Service Fabric](/azure/templates/microsoft.servicefabric/clusters) |
| Máquinas Virtuais |Sim |[VM REST](/rest/api/compute/virtualmachines) |[Recursos da VM](/azure/templates/microsoft.compute/virtualmachines) |
| Máquinas virtuais (clássico) |Limitado |- |- |
| Aplicativo Remoto |Não |- |- |
| Serviços de Nuvem (clássico) |Limitado (veja abaixo) |- |- |

Máquinas virtuais (clássico) refere-se aos recursos que foram implantados por meio do modelo de implantação clássico, não por meio do modelo de implantação do Gerenciador de Recursos. Em geral, esses recursos não oferecem suporte a operações do Gerenciador de Recursos, mas existem algumas operações que foram habilitadas. Para saber mais sobre esses modelos de implantação, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](resource-manager-deployment-model.md). 

Os Serviços de Nuvem (clássicos) podem ser usados com outros recursos clássicos. No entanto, os recursos clássicos não aproveitarão todos os recursos do Resource Manager e não são uma boa opção para soluções futuras. Em vez disso, considere alterar a infraestrutura de aplicativos para usar os recursos dos namespaces Microsoft.Compute, Microsoft.Storage e Microsoft.Network.

## <a name="networking"></a>Rede
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- |
| Gateway de Aplicativo |Sim |[REST do Application Gateway](https://msdn.microsoft.com/library/azure/mt684939.aspx) | [Recursos do Gateway de Aplicativo](/azure/templates/microsoft.network/applicationgateways) |
| DNS |Sim |[DNS REST](/rest/api/dns) |[Recursos de DNS](/azure/templates/microsoft.network/dnszones) |
| ExpressRoute |Sim |[REST da Rota Expressa](https://msdn.microsoft.com/library/azure/mt586720.aspx) | [Recursos do ExpressRoute](/azure/templates/microsoft.network/expressroutecircuits) |
| Balanceador de carga |Sim |[REST do balanceador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[Recursos do Balanceador de Carga](/azure/templates/microsoft.network/loadbalancers) |
| Gerenciador de Tráfego |Sim |[REST do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Recursos do Gerenciador de Tráfego](/azure/templates/microsoft.network/trafficmanagerprofiles) |
| Redes Virtuais |Sim |[REST da Rede Virtual](https://msdn.microsoft.com/library/azure/mt163650.aspx) |[Recursos da Rede Virtual](/azure/templates/microsoft.network/virtualnetworks) |
| Gateway de Rede |Sim |[REST de Gateway de rede](https://msdn.microsoft.com/library/azure/mt163859.aspx) | [Recursos de Conexão](/azure/templates/microsoft.network/connections) <br /> [Recursos do Gateway de Rede Local](/azure/templates/microsoft.network/localnetworkgateways) <br /> [Recursos do Gateway de Rede Virtual](/azure/templates/microsoft.network/virtualnetworkgateways) |

## <a name="storage"></a>Armazenamento
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- | --- |
| Importação/Exportação | Sim | [REST de Importação/Exportação](/rest/api/storageimportexport/) | [Recursos de Importação/Exportação](/azure/templates/microsoft.importexport/jobs) |
| Armazenamento |Sim |[REST de armazenamento](/rest/api/storagerp) |[Recursos de Armazenamento](/azure/templates/microsoft.storage/storageaccounts) |
| StorSimple |Sim | | |

## <a name="databases"></a>Bancos de dados
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- | --- |
| Azure Cosmos DB |Sim |[REST do Azure Cosmos DB](/rest/api/documentdbresourceprovider) |[Recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/databaseaccounts) |
| Cache Redis |Sim | [REST do Cache Redis](/rest/api/redis) |[Recursos do Redis](/azure/templates/microsoft.cache/redis) |
| Banco de Dados SQL |Sim |[REST do Banco de Dados SQL](/rest/api/sql) |[Recursos do Banco de Dados SQL](/azure/templates/microsoft.sql/servers) |
| SQL Data Warehouse |Sim | | |

## <a name="web--mobile"></a>Web e serviços móveis
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- |
| Aplicativos de API |Sim | [REST do Serviço de Aplicativo](/rest/api/appservice) |[Recursos da Web](/azure/templates/microsoft.web/sites) |
| Gerenciamento de API |Sim |[REST de gerenciamento de API](/rest/api/apimanagement) |[Recursos de Gerenciamento de API](/azure/templates/microsoft.apimanagement/service) |
| Registro de Certificado | Sim | [REST de Registro de Certificado](/rest/api/appservice/appservicecertificateorders) | [Recursos de Registro de Certificado](/azure/templates/microsoft.certificateregistration/certificateorders)  |
| Content Moderator |Sim | | | |
| Registro de Domínio | Sim | [Registro de Domínio](/rest/api/appservice/domains) | [Recursos de Registro de Domínio](/azure/templates/microsoft.domainregistration/domains)  |
| Aplicativo de Funções |Sim | [REST do Aplicativo de Funções](/rest/api/appservice) | [Recursos da Web](/azure/templates/microsoft.web/sites) |
| Aplicativos Lógicos |Sim |[REST dos Aplicativos Lógicos](/rest/api/logic) |[Recursos do Aplicativo Lógico](/azure/templates/microsoft.logic/workflows) |
| Aplicativos Móveis |Sim | [REST do Serviço de Aplicativo](/rest/api/appservice) | [Recursos da Web](/azure/templates/microsoft.web/sites) |
| Compromisso de mobilidade |Sim |[REST do Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |
| Pesquisar |Sim |[REST de pesquisa](/rest/api/searchservice) | [Recursos de Pesquisa](/azure/templates/microsoft.search/searchservices) |
| Aplicativos Web |Sim | [REST dos Aplicativos Web](/rest/api/appservice/webapps) | [Recursos da Web](/azure/templates/microsoft.web/sites) |

## <a name="intelligence--analytics"></a>Inteligência + Análises
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo | 
| --- | --- | --- | --- |
| Serviços de análise | Sim | [REST do Analysis Services](/rest/api/analysisservices) | [Recursos do Analysis Services](/azure/templates/microsoft.analysisservices/servers) |
| Serviços Cognitivos |Sim | [REST dos Serviços Cognitivos](/rest/api/cognitiveservices) |[Recursos dos Serviços Cognitivos](/azure/templates/microsoft.cognitiveservices/accounts) |
| Catálogo de Dados |Sim |[REST do Catálogo de Dados](/rest/api/datacatalog) |[Esquema do Catálogo de Dados](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |
| Data Factory |Sim |[REST do Data Factory](/rest/api/datafactory) | |
| Análises Data Lake |Sim | [REST do Data Lake](/rest/api/datalakeanalytics) |[Recursos do Data Lake Analytics](/azure/templates/microsoft.datalakeanalytics/accounts) |
| Repositório Data Lake |Sim |[REST do Data Lake Store](/rest/api/datalakestore) |[Recursos do Data Lake Store](/azure/templates/microsoft.datalakestore/accounts) |
| HDInsights |Sim |[REST do HDInsights](/rest/api/hdinsight) | |
| Machine Learning |Sim |[REST do Aprendizado de Máquina](/rest/api/machinelearning) |[Recursos do Machine Learning](/azure/templates/microsoft.machinelearning/commitmentplans) |
| Stream Analytics |Sim |[Análise de fluxo REST](/rest/api/streamanalytics) | |
| Power BI |Sim |[REST do Power BI Embedded](/rest/api/powerbiembedded) |[Recursos do Power BI](/azure/templates/microsoft.powerbi/workspacecollections) |


## <a name="internet-of-things"></a>Internet das coisas
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- |
| Hub de evento |Sim |[REST do Hub de Eventos](/rest/api/eventhub) |[Recursos do Hub de Eventos](/azure/templates/microsoft.eventhub/namespaces) |
| IoTHubs |Sim |[REST do Hub IoT](/rest/api/iothub) |[Recursos do Hub IoT](/azure/templates/microsoft.devices/iothubs) |
| Hubs de Notificação |Sim |[REST do Hub de notificação](/rest/api/notificationhubs) |[Recursos do Hub de Notificação](/azure/templates/microsoft.notificationhubs/namespaces) |

## <a name="media--cdn"></a>Mídia e CDN
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- |
| CDN |Sim |[REST CDN](/rest/api/cdn) |[Recursos da CDN](/azure/templates/microsoft.cdn/profiles) |
| Serviço de mídia |Sim |[REST dos Serviços de Mídia](/rest/api/media) |[Recursos de Mídia](/azure/templates/microsoft.media/mediaservices) |

## <a name="enterprise-integration"></a>Integração Empresarial
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- |
| Serviços do BizTalk |Sim | |[Esquema do BizTalk](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Retransmissão | Sim |  | [Recursos de retransmissão](/azure/templates/microsoft.relay/namespaces) |
| Barramento de Serviço |Sim |[REST do Barramento de Serviço](/rest/api/servicebus) |[Recursos do Barramento de Serviço](/azure/templates/microsoft.servicebus/namespaces) |

## <a name="identity--access-management"></a>Gerenciamento de acesso e identidade
O Active Directory do Azure funciona com o Gerenciador de Recursos para habilitar o controle de acesso baseado em função para sua assinatura. Para saber mais sobre como usar o controle de acesso baseado em funções e o Active Directory do Azure, consulte [Controle de Acesso baseado em Funções do Azure](../active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Serviços para Desenvolvedores
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- |
| Monitoramento |Sim |[REST do Monitor](/rest/api/monitor) |[Recursos do Insights](/azure/templates/microsoft.insights/alertrules) |
| Bing Mapas |Sim | | |
| Laboratórios de Desenvolvimento/Teste |Sim | [REST do DevTest Labs](/rest/api/dtl) |[Recursos do DevTest Labs](/azure/templates/microsoft.devtestlab/labs) |
| Conta do Visual Studio |Sim | |[Esquema do Visual Studio](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## <a name="management-and-security"></a>Gerenciamento e segurança
| O Barramento de | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- |
| Supervisor | Sim | [REST do Assistente](/rest/api/advisor/) | - |
| Automação |Sim |[Automação REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Recursos de automação](/azure/templates/microsoft.automation/automationaccounts) |
| Cobrança | Sim | [REST de Cobrança](/rest/api/billing/) | - |
| Cofre da Chave |Sim |[REST do Cofre da Chave](/rest/api/keyvault) |[Recursos do Key Vault](/azure/templates/microsoft.keyvault/vaults) |
| Insights Operacionais |Sim | | |
| Serviço de Recuperação |Sim |[REST dos Serviços de Recuperação](/rest/api/recoveryservices) |[Recursos dos Serviços de Recuperação](/azure/templates/microsoft.recoveryservices/vaults) |
| Agendador |Sim |[REST do Agendador](/rest/api/scheduler) |[Recursos do Agendador](/azure/templates/microsoft.scheduler/jobcollections) |
| Segurança |Sim |[REST de Segurança](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |
| Gerenciamento do Servidor | Sim | [REST de Gerenciamento do Servidor](/rest/api/servermanagement/) | [Recursos de Gerenciamento do Servidor](/azure/templates/microsoft.servermanagement/gateways) |

## <a name="resource-manager"></a>Gerenciador de Recursos
| Recurso | Gerenciador de Recursos habilitado | API REST | Formato de modelo |
| --- | --- | --- | --- | --- |
| Autorização |Sim |[REST da Autorização](/rest/api/authorization) |[Recursos de Autorização](/azure/templates/microsoft.authorization/locks) |
| Recursos |Sim |[REST dos Recursos](/rest/api/resources) |[Recursos de Implantação](/azure/templates/microsoft.resources/deployments) |

## <a name="resource-providers-and-types"></a>Provedores e tipos de recursos
Ao implantar recursos, com frequência você precisa recuperar informações sobre os provedores e tipos de recursos. Você pode recuperar essas informações por meio da API REST, do Azure PowerShell ou da CLI do Azure.

Para trabalhar com um provedor de recursos, o provedor de recursos deve ser registrado com sua conta. Por padrão, vários provedores de recursos são automaticamente registrados; no entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. Os exemplos nesta seção mostram como obter o status do registro de um provedor de recursos e registrar o provedor de recursos.

### <a name="portal"></a>Portal
Veja uma lista dos provedores de recursos com suporte selecionando **Provedores de recursos** na folha da assinatura. Para registrar sua assinatura em um provedor de recursos, selecione o link **Registrar**.
   
![listar provedores de recursos](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>API REST
Para obter todos os provedores de recursos disponíveis, incluindo seus tipos, suas localizações, suas versões de API e seu status do registro, use a operação [List all resource providers](https://docs.microsoft.com/rest/api/resources/providers#Providers_List). Se você precisar registrar um provedor de recursos, consulte [Registrar uma assinatura em um provedor de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register).

### <a name="powershell"></a>PowerShell
O exemplo a seguir mostra como obter todos os provedores de recursos disponíveis.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```


O exemplo a seguir mostra como obter os tipos de recursos para um provedor de recursos específico.

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
```

Para registrar um provedor de recursos, forneça o namespace:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement
```

### <a name="azure-cli"></a>CLI do Azure
O exemplo a seguir mostra como obter todos os provedores de recursos disponíveis.

```azurecli
az provider list
```

Você pode exibir as informações de um provedor de recursos específico para um arquivo com o comando a seguir:

```azurecli
az provider show --namespace Microsoft.Web
```

Para registrar um provedor de recursos, forneça o namespace:

```azurecli
az provider register --namespace Microsoft.ServiceBus
```

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

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

### <a name="azure-cli"></a>CLI do Azure
O exemplo a seguir mostra como obter as regiões com suporte para sites da Web.

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```


## <a name="supported-api-versions"></a>Versões de API com suporte
Ao implantar um modelo, você deve especificar uma versão de API a ser usada para criar cada recurso. A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST. Portanto, a versão da API especificada em seu modelo afetará quais propriedades estarão disponíveis para você durante a criação do modelo. Em geral, você deve selecionar a versão mais recente da API ao criar novos modelos. Para os modelos existentes, você pode decidir se deseja continuar usando uma versão de API anterior ou atualizar o modelo para a versão mais recente para aproveitar os novos recursos.

### <a name="portal"></a>Portal
Você pode determinar as versões da API com suporte da mesma maneira que determinou as regiões com suporte (mostrado anteriormente).

### <a name="rest-api"></a>API REST
Para descobrir quais versões de API está disponíveis para os tipos de recurso, use a operação [Listar todos os provedores de recursos](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) . 

### <a name="powershell"></a>PowerShell
O exemplo a seguir mostra como obter as versões de API disponíveis para um tipo de recurso específico.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

A saída deverá ser semelhante a:

```powershell
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
```

### <a name="azure-cli"></a>CLI do Azure
Você pode obter as versões de API disponíveis para um provedor de recursos com o seguinte comando:

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a criação de modelos do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
* Para saber mais sobre como implantar recursos, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).


