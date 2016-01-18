<properties
   pageTitle="Serviços, regiões, esquemas e versões com suporte do Gerenciador de Recursos | Microsoft Azure"
   description="Descreve os provedores de recursos que oferecem suporte ao Gerenciador de Recursos, aos respectivos esquemas e versões de API disponíveis, bem como às regiões que podem hospedar os recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/22/2015"
   ms.author="tomfitz"/>

# Provedores, regiões, versões de API e esquemas do Gerenciador de Recursos

O Gerenciador de Recursos do Azure fornece uma nova maneira de implantar e gerenciar os serviços que compõem seus aplicativos. A maioria (não todos) dos serviços suporta o Gerenciador de Recursos e alguns serviços suportam o Gerenciador de Recursos apenas parcialmente. A Microsoft habilitará o Gerenciador de Recursos para todos os serviços importantes para soluções futuras, mas, até que o suporte seja consistente, você precisa saber o status atual de cada serviço. Este tópico fornece uma lista de provedores de recursos com suporte para o Gerenciador de Recursos do Azure.

Ao implantar seus recursos, você também precisa saber quais são as regiões que oferecem suporte a esses recursos e quais versões de API estão disponíveis para os recursos. A seção [Regiões com suporte](#supported-regions) mostra como descobrir quais regiões funcionarão para sua assinatura e seus recursos. A seção [Versões de API com suporte](#supported-api-versions) mostra como determinar quais versões de API você pode usar.

Para ver quais serviços têm suporte no Portal do Azure e no portal clássico, confira o [Gráfico de disponibilidade do Portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/).

As tabelas a seguir listam quais serviços suportam a implantação e o gerenciamento por meio do Gerenciador de Recursos e quais não. A coluna denominada **Mover Recursos** indicam se os recursos desse tipo podem ser movidos para um novo grupo de recursos e uma nova assinatura. O link na coluna **Modelos de Início Rápido** envia uma consulta ao repositório Modelos de Início Rápido do Azure para o provedor de recursos especificado. Os modelos de início rápido são adicionados e atualizados com frequência. A presença de um link para um serviço específico não significa necessariamente que a consulta retornará modelos do repositório.


## Computação

| O Barramento de | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | ------------------------ | -------------- |-------- | ------ | ------ |
| Máquinas Virtuais | Sim | Não | [Criar VM](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [Microsoft.Compute](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%22&type=Code) |
| Batch | Sim | Sim | [REST do Lote](https://msdn.microsoft.com/library/azure/dn820158.aspx) | | [Microsoft.Batch](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Batch%22&type=Code) |
| Serviços de ciclo de vida do Dynamics | Sim | | | | [Microsoft.DynamicsLcs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DynamicsLcs%22&type=Code)
| Service Fabric (visualização) | Sim | | [Service Fabric Rest](https://msdn.microsoft.com/library/azure/dn707692.aspx) | | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Máquinas virtuais (clássico) | Limitado | Parcial (veja abaixo) | - | - |
| Aplicativo Remoto | Não | - | - | - |

Máquinas virtuais (clássico) refere-se aos recursos que foram implantados por meio do modelo de implantação clássico, não por meio do modelo de implantação do Gerenciador de Recursos. Em geral, esses recursos não oferecem suporte a operações do Gerenciador de Recursos, mas existem algumas operações que foram habilitadas. Para saber mais sobre esses modelos de implantação, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](resource-manager-deployment-model.md).

Recursos de máquinas virtuais (clássico) podem ser movidos para um novo grupo de recursos, mas não uma nova assinatura.

## Rede

| O Barramento de | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Application Gateway | Sim | | | | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS | Sim | | [Criar zona DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| Balanceador de carga | Sim | | [Criar balanceador de carga](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Redes Virtuais | Sim | Não | [Criar Rede Virtual](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Gerenciador de Tráfego | Sim | | [Criar perfil do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/mt163581.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Rota Expressa | Sim | Não | [REST da Rota Expressa](https://msdn.microsoft.com/library/azure/mt586720.aspx) | | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |

## Armazenamento de dados

| O Barramento de | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | ------- | -------------- | -------- | ------ | ------- | ------ |
| Banco de Dados de Documentos | Sim | Sim | [REST do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Armazenamento | Sim | Não | [Criar Armazenamento](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [Conta de armazenamento](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| Cache Redis | Sim | Sim | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Banco de Dados SQL | Sim | Sim | [Criar banco de dados](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Pesquisar | Sim | Sim | [REST de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) | | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| SQL Data Warehouse | Sim | | | |
| StorSimple | Não | - | - | - |
| Cache gerenciado | Não | - | - | - |

## Web e serviços móveis

| O Barramento de | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Gerenciamento da API | Sim | Sim | [Criar API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Aplicativos de API | Sim | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) | [Aplicativos de API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Aplicativos Web | Sim | Sim, com limitações (veja abaixo) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |
| Hubs de Notificação | Sim | Sim | [Criar Hub de Notificação](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |
| Aplicativos Lógicos | Sim | Sim | | | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Compromisso de mobilidade | Sim | Sim | | | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |

Ao trabalhar com aplicativos Web, você não pode mover um plano de Serviço de Aplicativo. Para mover os aplicativos Web, as opções são:

- Mova todos os recursos de um grupo de recursos para um grupo de recursos diferente, se o grupo de recursos de destino ainda não tiver recursos Microsoft.Web.
- Mova os aplicativos Web para um grupo de recursos diferente, mas mantenha o plano de Serviço de Aplicativo no grupo de recursos original.

## Análise

| O Barramento de | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| Hub de evento | Sim | | [Criar Hub de eventos](https://msdn.microsoft.com/library/azure/dn790676.aspx) | | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| Análise de fluxo | Sim | | [Análise de fluxo REST](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | [Microsoft.StreamAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.StreamAnalytics%22&type=Code) |
| HDInsights | Sim | Sim | [Criar cluster](https://msdn.microsoft.com/library/azure/mt622302.aspx) | | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Data Factory | Sim | Sim | [Criar fábrica de dados](https://msdn.microsoft.com/library/azure/dn906717.aspx) | | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Aprendizado de Máquina | Não | - | - | - | 
| Catálogo de Dados | Não | - | - | - |

## Mídia e CDN

| O Barramento de | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| CDN | Sim | | [REST CDN](https://msdn.microsoft.com/library/azure/mt634456.aspx) | | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Serviço de mídia | Não | | | |


## Integração híbrida

| O Barramento de | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Serviços do BizTalk | Sim | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | [Microsoft.BizTalkServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BizTalkServices%22&type=Code) |
| Barramento de Serviço | Sim | | [REST do Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh780717.aspx) | | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |
| Backup | Não | - | - | - |
| Recuperação de Site | Não | - | - | - |

## Gerenciamento de acesso e identidade 

| O Barramento de | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Active Directory do Azure | Não | - | - | - |
| Actice Directory B2C do Azure | Não | - | - | - |
| Multi-Factor Authentication | Não | - | - | - |

## Serviços para Desenvolvedores 

| O Barramento de | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Application Insights | Sim | Não | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Mapas | Sim | | | | [Microsoft.BingMaps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BingMaps%22&type=Code) |
| Laboratórios de Desenvolvimento/Teste (visualização) | Sim | | | [2015-05-21-visualização](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-05-21-preview/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Conta do Visual Studio | Sim | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | [Microsoft.VisualStudio](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.VisualStudio%22&type=Code) |

## Gerenciamento e segurança

| O Barramento de | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Automação | Sim | Sim | | | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Cofre da Chave | Sim | Sim | [REST do Cofre da Chave](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Cofre da chave](resource-manager-template-keyvault.md)<br />[Segredo do cofre da chave](resource-manager-template-keyvault-secret.md) | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Agendador | Sim | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Insights Operacionais | Sim | Sim | | | [Microsoft.OperationalInsights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.OperationalInsights%22&type=Code) |
| IoTHubs | Sim | | [Criar IoT Hub](https://msdn.microsoft.com/library/azure/mt589013.aspx) | | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Segurança (visualização) | Sim | | | | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## Gerenciador de Recursos

| Recurso | Gerenciador de Recursos habilitado | Mover recursos | API REST | Esquema | Modelos de Início Rápido |
| ------- | ------- | -------- | -------------- | -------- | ------ | ------ |
| Autorização | Sim | N/D | [Gerenciamento de bloqueios](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Controle de acesso baseado em função](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Bloqueio de recurso](resource-manager-template-lock.md)<br />[Atribuições de função](resource-manager-template-role.md) | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Recursos | Sim | N/D | [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Link de recursos](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## Regiões com suporte

Durante a implantação de recursos, você normalmente precisa especificar uma região para os recursos. O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações em sua assinatura que impedem o uso de algumas regiões que oferecem suporte aos recursos. Essas limitações podem estar relacionadas a problemas tributários de seu país de residência ou ao resultado de uma política colocada pelo seu administrador de assinatura para usar somente determinadas regiões.

Para obter uma lista completa de todas as regiões com suporte para todos os serviços do Azure, confira [Serviços por região](https://azure.microsoft.com/regions/#services). No entanto, essa lista pode incluir regiões para as quais sua assinatura não oferece suporte. É possível determinar as regiões para um determinado tipo de recurso com suporte da sua assinatura executando um dos comandos a seguir.

### API REST

Para descobrir quais regiões estão disponíveis para um tipo de recurso específico em sua assinatura, use a operação [Listar todos os fornecedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

O exemplo a seguir mostra como obter as regiões com suporte para sites da web usando o Azure PowerShell 1.0. Para saber mais sobre a versão 1.0, confira [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
A saída será semelhante a:

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

Para o Azure PowerShell 0.9.8, use o seguinte comando:

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

### CLI do Azure

O exemplo a seguir retorna todos os locais com suporte para cada tipo de recurso.

    azure location list

Você também pode filtrar os resultados de local com uma ferramenta como **jq**. Para saber mais sobre ferramentas, como jq, confira [Ferramentas úteis para interagir com o Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Que retorna:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## Versões de API com suporte

Ao implantar um modelo, você deve especificar uma versão de API a ser usada para criar cada recurso. A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Como um provedor de recursos habilita novos recursos, ele lançará uma nova versão da API REST. Portanto, a versão da API especificada em seu modelo afetará quais propriedades estarão disponíveis para você durante a criação do modelo. Em geral, você deve selecionar a versão mais recente da API ao criar novos modelos. Para os modelos existentes, você pode decidir se deseja continuar usando uma versão de API anterior ou atualizar o modelo para a versão mais recente para aproveitar os novos recursos.

### API REST

Para descobrir quais versões de API estão disponíveis para os tipos de recurso, use a operação [Listar todos os fornecedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

O exemplo a seguir mostra como obter as versões de API disponíveis para um tipo de recurso determinado usando o Azure PowerShell 1.0.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
A saída será semelhante a:
    
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

Para o Azure PowerShell 0.9.8, use:

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### CLI do Azure

Você pode salvar as informações (incluindo as versões disponíveis de API) para um provedor de recursos em um arquivo com o comando a seguir.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Você pode abrir o arquivo e localizar o elemento **apiVersions**

## Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
- Para saber mais sobre como implantar recursos, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_1223_2015-->
