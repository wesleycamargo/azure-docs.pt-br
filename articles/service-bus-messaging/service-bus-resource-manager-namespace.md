---
title: Criar um namespace das Mensagens do Barramento de Serviço usando modelo do Azure Resource Manager | Microsoft Docs
description: Usar modelo do Azure Resource Manager para criar um namespace das Mensagens do Barramento de Serviço
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: e925ea91518d8f093cd270e238b7ffd09674e726
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844085"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Criar um namespace do Barramento de Serviço usando um modelo do Azure Resource Manager
Neste início rápido, você cria um modelo do Azure Resource Manager que cria um namespace do Barramento de Serviço do tipo **Messaging** com um SKU **Standard**. O artigo também define os parâmetros que são especificados para execução da implantação. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades. Para obter mais informações sobre a criação de modelos, consulte [Criação de Modelos do Azure Resource Manager][Authoring Azure Resource Manager templates]. Para ver o modelo completo, confira o [Modelo de namespace do Barramento de Serviço][Service Bus namespace template] no GitHub.

> [!NOTE]
> Os modelos do Azure Resource Manager a seguir estão disponíveis para download e implantação. 
> 
> * [Criar um namespace do Barramento de Serviço com fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um namespace do Barramento de Serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
> * [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um namespace do Barramento de Serviço com tópico, assinatura e regra](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][Azure Quickstart Templates] e pesquise por Barramento de Serviço.

## <a name="quick-deployment"></a>Implantação rápida
Para executar o exemplo sem gravar nenhum JSON nem executar o comando do PowerShell/CLI, selecione o botão a seguir:

[![Implantar no Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

Para criar e implantar o modelo manualmente, pesquise as seções a seguir deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, você precisa de uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.

Se você quiser usar **Azure PowerShell** para implantar o modelo do Resource Manager [instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-5.7.0).

Se você quiser usar o **Azure CLI** para implantar o modelo do Resource Manager, [instale o Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Crie o modelo JSON do Resource Manager 
Crie um arquivo JSON denominado **MyServiceBusNamespace.json** com o seguinte conteúdo: 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

Este modelo cria um namespace do Barramento de Serviço padrão. Saiba mais sobre a sintaxe e as propriedades de JSON na referência de modelo [namespaces](/azure/templates/microsoft.servicebus/namespaces).

## <a name="create-the-parameters-json"></a>Crie os parâmetros JSON
O modelo que você criou na etapa anterior tem uma seção chamada `Parameters`. Defina os parâmetros para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente de destino. Esse modelo define os seguintes parâmetros: **serviceBusNamespaceName**, **serviceBusSku** e **local**. Para saber mais sobre os SKUs do Barramento de Serviço, consulte [SKUs do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/) para criar.

Crie um arquivo JSON denominado **MyServiceBusNamespace-Parameters.json** com o seguinte conteúdo: 

> [!NOTE] 
> Especifique um nome para seu namespace do Barramento de Serviço. 


```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>Use o Azure PowerShell para implantar o modelo

### <a name="sign-in-to-azure"></a>Entrar no Azure
1. Iniciar o Azure PowerShell

2. Execute o comando a seguir para entrar no Azure:

   ```azurepowershell
   Login-AzureRmAccount
   ```
3. Se você tiver emita os seguintes comandos para definir o contexto da assinatura atual:

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>Implantação de recursos
Para implantar os recursos usando o Azure PowerShell, alterne para a pasta onde você salvou os arquivos JSON e execute os seguintes comandos:

> [!IMPORTANT]
> Especifique um nome para o grupo de recursos do Azure como um valor para $ resourceGroupName antes de executar os comandos. 

1. Declare uma variável para o nome do grupo de recursos e especifique um valor para ele. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Crie um grupo de recursos do Azure.

    ```azurepowershell
    New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ```
3. Implante o modelo do Resource Manager. Especifique os nomes de implantação propriamente dita, o grupo de recursos, o arquivo JSON para o modelo, o arquivo JSON para os parâmetros

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Use a CLI do Azure para implantar o modelo

### <a name="sign-in-to-azure"></a>Entrar no Azure

1. Execute o comando a seguir para entrar no Azure:

    ```azurecli
    az login
    ```
2. Defina o contexto da assinatura atual. Substitua `MyAzureSub` pelo nome da assinatura do Azure que deseja usar:

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>Implantação de recursos
Para implantar os recursos usando a CLI do Azure, alterne para a pasta com os arquivos JSON e execute os seguintes comandos:

> [!IMPORTANT]
> Especifique um nome para o grupo de recursos do Azure no comando az group create. .

1. Crie um grupo de recursos do Azure. 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Implante o modelo do Resource Manager. Especifique os nomes de implantação propriamente dita, o grupo de recursos, o arquivo JSON para o modelo, o arquivo JSON para os parâmetros.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um namespace do Barramento de Serviço. Consulte outros guias de início rápido para saber como criar filas, tópicos/assinaturas e como usá-los: 

- [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
- [Introdução aos tópicos do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
