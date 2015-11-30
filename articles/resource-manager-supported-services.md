<properties
   pageTitle="Serviços e regiões do Gerenciador de Recursos com suporte | Microsoft Azure"
   description="Descreve os provedores de recursos que suportam o Gerenciador de Recursos e as regiões que podem hospedar os recursos."
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
   ms.date="11/18/2015"
   ms.author="tomfitz"/>

# Suporte do Gerenciador de Recursos do Azure para serviços, regiões e versões de API

O Gerenciador de Recursos do Azure fornece uma nova maneira de implantar e gerenciar os serviços que compõem seus aplicativos. A maioria (não todos) dos serviços suporta o Gerenciador de Recursos e alguns serviços suportam o Gerenciador de Recursos apenas parcialmente. A Microsoft habilitará o Gerenciador de Recursos para todos os serviços importantes para soluções futuras, mas, até que o suporte seja consistente, você precisa saber o status atual de cada serviço. Este tópico fornece uma lista de provedores de recursos com suporte para o Gerenciador de Recursos do Azure.

Ao implantar seus recursos, você também precisa saber quais são as regiões que oferecem suporte a esses recursos e quais versões de API estão disponíveis para os recursos. A seção [Regiões com suporte](#supported-regions) mostra como descobrir quais regiões funcionarão para sua assinatura e seus recursos. A seção [Versões de API com suporte](#supported-api-versions) mostra como determinar quais versões de API você pode usar.

As tabelas a seguir listam quais serviços suportam a implantação e o gerenciamento por meio do Gerenciador de Recursos e quais não. A coluna denominada **Mover recursos** refere-se a se recursos desse tipo podem ser movidos para um novo grupo de recursos e uma nova assinatura. A coluna denominada **Portal de visualização** indica se você pode criar o serviço por meio do portal de visualização.


## Computação

| O Barramento de | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| Máquinas Virtuais | Sim | Sim, várias opções | Não | [Criar VM](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| Batch | Sim | [Sim (somente clássicas)](https://portal.azure.com/#create/Microsoft.BatchAccount) | | [REST do Lote](https://msdn.microsoft.com/library/azure/dn820158.aspx) | |
| Serviços de ciclo de vida do Dynamics | Sim | Não | | | |
| Máquinas Virtuais (clássicas) | Limitado | Sim, várias opções | Parcial (veja abaixo) | - | - | | Aplicativo Remoto | Não | Não | - | - | - | | Service Fabric | Não | Não | - | - | - |

Máquinas virtuais (clássico) refere-se aos recursos que foram implantados por meio do modelo de implantação clássico, não por meio do modelo de implantação do Gerenciador de Recursos. Em geral, esses recursos não oferecem suporte a operações do Gerenciador de Recursos, mas existem algumas operações que foram habilitadas. Para obter mais informações sobre esses modelos de implantação, veja [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](resource-manager-deployment-model.md).

Recursos de máquinas virtuais (clássico) podem ser movidos para um novo grupo de recursos, mas não uma nova assinatura.

## Rede

| O Barramento de | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Application Gateway | Sim | | | | |
| DNS | Sim | | | [Criar zona DNS](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Balanceador de carga | Sim | | | [Criar balanceador de carga](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Redes Virtuais | Sim | [Sim](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) | Não | [Criar Rede Virtual](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| Gerenciador de Tráfego | Sim | Não | | [Criar perfil do Gerenciador de Tráfego](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| Rota Expressa | Sim | Não | Não | [REST da Rota Expressa](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |

## Armazenamento de dados

| O Barramento de | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| Banco de Dados de Documentos | Sim | [Sim](https://portal.azure.com/#create/Microsoft.DocumentDB) | Sim | [REST do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| Armazenamento | Sim | [Sim](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) | Não | [Criar Armazenamento](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [Conta de armazenamento](resource-manager-template-storage.md) |
| Cache Redis | Sim | [Sim](https://portal.azure.com/#create/Microsoft.Cache.1.0.4) | Sim | | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| Banco de dados SQL | Sim | [Sim](https://portal.azure.com/#create/Microsoft.SQLDatabase.0.5.9-preview) | Sim | [Criar banco de dados](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| Pesquisar | Sim | [Sim](https://portal.azure.com/#create/Microsoft.Search) | Sim | [REST de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| SQL Data Warehouse | Sim | [Sim](https://portal.azure.com/#create/Microsoft.SQLDataWarehouse.0.1.12-preview) | | | |
| StorSimple | Não | Não | - | - | - | | Cache gerenciado | Não | Não | - | - | - |

## Web e serviços móveis

| O Barramento de | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Gerenciamento da API | Sim | Não | Sim | [Criar API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| Aplicativos de API | Sim | [Sim](https://portal.azure.com/#create/microsoft_com.ApiApp.0.2.0-preview) | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| Aplicativos Web | Sim | [Sim](https://portal.azure.com/#create/Microsoft.WebSite) | Sim, com limitações (veja abaixo) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| Hubs de Notificação | Sim | [Sim](https://portal.azure.com/#create/Microsoft.NotificationHub) | | [Criar Hub de Notificação](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| Aplicativos Lógicos | Sim | [Sim](https://portal.azure.com/#create/Microsoft.EmptyWorkflow.0.2.0-preview) | | | |
| Compromisso de mobilidade | Sim | Não | Sim | | |

Ao trabalhar com aplicativos Web, você não pode mover um plano de Serviço de Aplicativo. Para mover os aplicativos Web, as opções são:

- Mova todos os recursos de um grupo de recursos para um grupo de recursos diferente, se o grupo de recursos de destino ainda não tiver recursos Microsoft.Web.
- Mova os aplicativos Web para um grupo de recursos diferente, mas mantenha o plano de Serviço de Aplicativo no grupo de recursos original.

## Análise

| O Barramento de | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Hub de evento | Sim | Não | | [Criar Hub de eventos](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| Análise de fluxo | Sim | [Sim](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) | | | |
| HDInsights | Sim | [Sim](https://portal.azure.com/#create/Microsoft.HDInsightCluster) | | | |
| Data Factory | Sim | [Sim](https://portal.azure.com/#create/Microsoft.DataFactory) | Sim | [Criar fábrica de dados](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| Aprendizado de Máquina | Não | Não | - | - | - | | Catálogo de Dados | Não | Não | - | - | - |

## Mídia e CDN

| O Barramento de | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| CDN | Sim (visualização) | Não | | | |
| Serviço de mídia | Não | Não | | | |


## Integração híbrida

| O Barramento de | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Serviços do BizTalk | Sim | Não | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| Barramento de Serviço | Sim | Não | | [REST do Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |
| Backup | Não | Não | - | - | - | | Recuperação de Site | Não | Não | - | - | - |

## Gerenciamento de acesso e identidade 

| O Barramento de | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Active Directory do Azure | Não | Não | - | - | - | | Actice Directory B2C do Azure | Não | Não | - | - | - | | Autenticação multifator | Não | Não | - | - | - |

## Serviços para Desenvolvedores 

| O Barramento de | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | Sim | [Sim](https://portal.azure.com/#create/Microsoft.AppInsights.0.2.3-preview) | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Bing Mapas | Sim | [Sim](https://portal.azure.com/#create/bingmaps.mapapis.1.0.4) | | | |
| Conta do Visual Studio | Sim | | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## Gerenciamento 

| O Barramento de | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| Automação | Sim | [Sim](https://portal.azure.com/#create/Microsoft.AutomationAccount.1.0.2-preview) | | | |
| Cofre da Chave | Sim | Não | Sim | [REST do Cofre da Chave](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| Agendador | Sim | Não | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Insights Operacionais | Sim | Não | Sim | | |
| IoTHubs | Sim | [Sim](https://portal.azure.com/#create/Microsoft.IotHub) | | | |

## Gerenciador de Recursos

| Recurso | Gerenciador de Recursos habilitado | Portal de Visualização | Mover recursos | API REST | Esquema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Autorização | Sim | N/D | N/D | [Bloqueios de gerenciamento](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Controle de acesso baseado em função](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Bloqueio de recurso](resource-manager-template-lock.md)<br />[Atribuições de função](resource-manager-template-role.md) |
| Recursos | Sim | N/D | N/D | [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Link de recursos](resource-manager-template-links.md) |


## Regiões com suporte

Durante a implantação de recursos, você normalmente precisa especificar uma região para os recursos. O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações em sua assinatura que impedem o uso de algumas regiões que oferecem suporte aos recursos. Essas limitações podem estar relacionadas a problemas tributários de seu país de residência ou ao resultado de uma política colocada pelo seu administrador de assinatura para usar somente determinadas regiões.

Para obter uma lista completa de todas as regiões com suporte para todos os serviços do Azure, veja [Serviços por região](https://azure.microsoft.com/regions/#services); no entanto, essa lista pode incluir regiões para as quais não há suporte em sua assinatura. É possível determinar as regiões para um determinado tipo de recurso com suporte da sua assinatura executando um dos comandos a seguir.

### API REST

Para descobrir quais regiões estão disponíveis para um tipo de recurso específico em sua assinatura, use a operação [Listar todos os provedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

O exemplo a seguir mostra como obter as regiões com suporte para sites da web usando o Azure PowerShell 1.0 Preview. Para saber mais sobre a versão 1.0 Preview, consulte [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)

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

Você também pode filtrar os resultados de local com uma ferramenta como **jq**. Para saber mais sobre ferramentas como jq, consulte [Ferramentas úteis para interagir com o Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

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

Para descobrir quais versões de API está disponíveis para os tipos de recurso, use a operação [Listar todos os provedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

O exemplo a seguir mostra como obter as versões de API disponíveis para um tipo de recurso determinado usando o Azure PowerShell 1.0 Preview.

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

- Para saber mais sobre a criação de modelos do Gerenciador de Recursos do Azure, consulte [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
- Para saber mais sobre como implantar recursos, consulte [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).

<!---HONumber=Nov15_HO4-->