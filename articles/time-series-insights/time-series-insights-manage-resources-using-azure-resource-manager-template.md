---
title: Como gerenciar seu ambiente do Azure Time Series Insights usando modelos do Azure Resource Manager | Microsoft Docs
description: Este artigo descreve como gerenciar seu ambiente do Azure Time Series Insights programaticamente usando o Azure Resource Manager.
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 12/08/2017
ms.openlocfilehash: b09d4a1aea56a4e306f80a1b43d519d313fd73ab
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Criar recursos do Time Series Insights usando modelos do Azure Resource Manager

Este artigo descreve como criar e implantar recursos do Time Series Insights usando modelos do Azure Resource Manager, o PowerShell e o provedor de recursos do Time Series Insights.

O Time Series Insights oferece suporte aos seguintes recursos:
   | Recurso | DESCRIÇÃO |
   | --- | --- |
   | Ambiente | Um ambiente do Time Series Insights é um agrupamento lógico de eventos que são lidos de agentes de evento, armazenados e disponibilizados para consulta. Para obter mais informações, confira [Planejar um ambiente do Azure Time Series Insights](time-series-insights-environment-planning.md) |
   | Origem do Evento | Uma fonte de evento é uma conexão com um agente de eventos de onde o Time Series Insights lê e ingere eventos no ambiente. As fontes de evento com suporte no momento são o Hub IoT e o Hub de Eventos. |
   | Conjuntos de dados de referência | Os conjuntos de dados de referência fornecem metadados sobre os eventos no ambiente. Os metadados nos conjuntos de dados de referência serão adicionados aos eventos durante a entrada. Os conjuntos de dados de referência são definidos como recursos por suas propriedades de chave de evento. Os metadados que compõem o conjunto de dados de referência são carregados ou modificados por meio de APIs de plano de dados. |
   | Política de acesso | As políticas de acesso concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e compartilhar consultas salvas e perspectivas associadas ao ambiente. Para obter mais informações, confira [Conceder acesso a dados em um ambiente do Time Series Insights usando o portal do Azure](time-series-insights-data-access.md) |

Um modelo do Resource Manager é um arquivo JSON que define a infraestrutura e a configuração de recursos em um grupo de recursos. Para obter mais informações, confira os documentos a seguir:

- [Visão geral do Azure Resource Manager - implantação de modelo](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)

O modelo de início rápido [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) é publicado no GitHub. Este modelo cria um ambiente do Time Series Insights, uma fonte de evento filho configurada para consumir eventos de um Hub de Eventos, além de políticas de acesso que concedem acesso aos dados do ambiente. Se um Hub de Eventos existente não for especificado, ele será criado com a implantação.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Implantar o modelo de início rápido localmente usando o PowerShell

O procedimento a seguir descreve como usar o PowerShell para implantar um modelo do Azure Resource Manager que cria um ambiente do Time Series Insights, uma fonte de evento filho configurada para consumir eventos de um Hub de Eventos e políticas de acesso que concedem acesso aos dados do ambiente. Se um Hub de Eventos existente não for especificado, ele será criado com a implantação.

O fluxo de trabalho é mais ou menos o seguinte:

1. Instale o PowerShell.
1. Crie o modelo e um arquivo de parâmetro.
1. No PowerShell, faça logon em sua conta do Azure.
1. Crie um novo grupo de recursos se já não tiver um.
1. Teste a implantação.
1. Implante o modelo.

### <a name="install-powershell"></a>Instalar o PowerShell

Instale o Azure PowerShell seguindo as instruções em [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Criar um modelo

Clone ou copie o modelo [201-timeseriesinsights-environment-with-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) do GitHub.

### <a name="create-a-parameters-file"></a>Criar um arquivo de parâmetros

Para criar um arquivo de parâmetros, copie o arquivo [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json).

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>Parâmetros obrigatórios

   | Parâmetro | DESCRIÇÃO |
   | --- | --- |
   | eventHubNamespaceName | O namespace do hub de eventos de origem. |
   | eventHubName | O nome do hub de eventos de origem. |
   | consumerGroupName | O nome do grupo consumidor que o serviço Time Series Insights usará para ler os dados do hub de eventos. **OBSERVAÇÃO:** para evitar a contenção de recursos, esse grupo consumidor deve ser dedicado ao serviço Time Series Insights e não deve ser compartilhado com outros leitores. |
   | environmentName | O nome do ambiente. O nome não pode incluir:  '<', '>', '%', '&', ':', '\\', '?', '/' nem caracteres de controle. Todos os outros caracteres são permitidos.|
   | eventSourceName | O nome do recurso filho da fonte do evento. O nome não pode incluir:  '<', '>', '%', '&', ':', '\\', '?', '/' nem caracteres de controle. Todos os outros caracteres são permitidos. |

#### <a name="optional-parameters"></a>Parâmetros opcionais

   | Parâmetro | DESCRIÇÃO |
   | --- | --- |
   | existingEventHubResourceId | Uma ID de recurso opcional de um Hub de Eventos existente que será conectada ao ambiente do Time Series Insights através da fonte do evento. **OBSERVAÇÃO:** o usuário que vai implantar o modelo deve ter privilégios para executar a operação listkeys no Hub de Eventos. Se nenhum valor for transmitido, um novo hub de eventos será criado pelo modelo. |
   | environmentDisplayName | Um nome amigável opcional para exibição nas ferramentas ou interfaces de usuário em vez do nome do ambiente. |
   | environmentSkuName | O nome da SKU. Para saber mais, confira a [Página de Preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | A capacidade da unidade da SKU. Para saber mais, confira a [Página de Preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | O intervalo de tempo mínimo em que os eventos do ambiente estarão disponíveis para consulta. O valor deve ser especificado no formato ISO 8601, por exemplo, "P30D" para uma política de retenção de 30 dias. |
   | eventSourceDisplayName | Um nome amigável opcional para exibição nas ferramentas ou interfaces de usuário em vez do nome da fonte do evento. |
   | eventSourceTimestampPropertyName | A propriedade de evento que será usada como carimbo de data/hora da fonte do evento. Se não for especificado um valor para timestampPropertyName, ou se for especificado null ou empty-string, a hora de criação do evento será usada. |
   | eventSourceKeyName | O nome da chave de acesso compartilhado que o serviço Time Series Insights usará para se conectar ao hub de eventos. |
   | accessPolicyReaderObjectIds | Uma lista de IDs de objeto dos usuários ou aplicativos no Azure AD que devem ter acesso de Leitor para o ambiente. O objectId da entidade de serviço pode ser obtido chamando os cmdlets **Get-AzureRMADUser** ou **Get-AzureRMADServicePrincipal Get**. Ainda não há suporte para a criação de uma política de acesso para grupos do Azure AD. |
   | accessPolicyContributorObjectIds | Uma lista de IDs de objeto dos usuários ou aplicativos no Azure AD que devem ter acesso de Colaborador para o ambiente. O objectId da entidade de serviço pode ser obtido chamando os cmdlets **Get-AzureRMADUser** ou **Get-AzureRMADServicePrincipal**. Ainda não há suporte para a criação de uma política de acesso para grupos do Azure AD. |

Por exemplo, o arquivo de parâmetros a seguir deve ser usado para criar um ambiente e uma fonte do evento que lê eventos de um hub de eventos existente. Ele também cria duas políticas de acesso que concedem acesso de Colaborador para o ambiente.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

Para saber mais, consulte o tópico [Parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Fazer logon no Azure e definir a assinatura do Azure

Em um prompt do PowerShell, execute o seguinte comando:

```powershell
Login-AzureRmAccount
```

Você precisará entrar em sua conta do Azure. Após o logon, execute o comando a seguir para exibir as assinaturas disponíveis:

```powershell
Get-AzureRMSubscription
```

Esse comando retorna uma lista de assinaturas do Azure disponíveis. Escolha uma assinatura para a sessão atual executando o comando a seguir. Substitua `<YourSubscriptionId>` pelo GUID da assinatura do Azure que deseja usar:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Definir o grupo de recursos

Se você não tiver um grupo de recursos existente, crie um novo com o comando **New-AzureRmResourceGroup**. Forneça o nome do grupo de recursos e local que você deseja usar. Por exemplo: 

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Se for bem-sucedido, um resumo do novo grupo de recursos será exibido.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Teste a implantação

Valide a implantação executando o cmdlet `Test-AzureRmResourceGroupDeployment`. Ao testar a implantação, forneça parâmetros exatamente como faria durante a sua execução.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Criar a implantação

Para criar a nova implantação, execute o cmdlet `New-AzureRmResourceGroupDeployment` e forneça os parâmetros necessários quando solicitado. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos e o caminho ou a URL para o arquivo do modelo. Caso o parâmetro **Mode** não esteja especificado, o valor padrão de **Incremental** será usado. Para saber mais, consulte [Implantações incrementais e completas](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

O comando abaixo solicita os cinco parâmetros obrigatórios na janela do PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Para especificar um arquivo de parâmetros em vez disso, use o comando a seguir:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Você também pode usar parâmetros embutidos quando executa o cmdlet de implantação. O comando é o seguinte:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para executar uma implantação [completa](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments), defina o parâmetro **Mode** como **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Verificar a implantação

Se os recursos forem implantados com êxito, um resumo da implantação será exibido na janela do PowerShell:

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Implantar o modelo de início rápido por meio do portal do Azure

A página inicial do modelo de início rápido no GitHub também inclui um botão **Implantar no Azure**. Clique nele para abrir uma página Implantação Personalizada no portal do Azure. Nessa página, você pode inserir ou selecionar valores para cada parâmetro nas tabelas de [parâmetros obrigatórios](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) ou [parâmetros opcionais](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters). Depois de preencher as configurações, clique no botão **Comprar** para iniciar a implantação de modelo.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank"> <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como gerenciar programaticamente os recursos do Time Series Insights usando APIs REST, confira [Gerenciamento do Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights-management/).
