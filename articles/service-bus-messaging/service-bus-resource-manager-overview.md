---
title: Criar recursos do Barramento de Serviço usando modelos do Azure Resource Manager | Microsoft Docs
description: Usar modelos do Azure Resource Manager para automatizar a criação de recursos do Barramento de Serviço
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 07/11/2016
ms.author: sethm

---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Criar recursos do Barramento de Serviço usando modelos do Azure Resource Manager
Este artigo mostra como criar e implantar recursos de Barramento de Serviço e de Hubs de Eventos usando modelos do Azure Resource Manager, o PowerShell e o provedor de recursos do Barramento de Serviço.

Os modelos do Azure Resource Manager ajudam você a definir os recursos a serem implantados em uma solução e a especificar os parâmetros e variáveis que lhe permitem inserir valores para diferentes ambientes. O modelo consiste em JSON e expressões que podem ser usados na construção de valores para sua implantação. Para obter informações detalhadas sobre a criação de modelos do Azure Resource Manager e uma discussão sobre o formato do modelo, consulte [Criando modelos do Azure Resource Manager](../resource-group-authoring-templates.md). 

> [!NOTE]
> Os exemplos neste artigo mostram como usar o Azure Resource Manager para criar um namespace do Barramento de Serviço e uma entidade de mensagens (fila). Para obter outros exemplos de modelo, visite a [Galeria de Modelos de Início Rápido do Azure][Galeria de Modelos de Início Rápido do Azure] e pesquise "Barramento de Serviço".
> 
> 

## <a name="service-bus-and-event-hubs-resource-manager-templates"></a>Modelos do Gerenciador de Recursos do Barramento de Serviço e dos Hubs de Eventos
Esses modelos do Azure Resource Manager de Barramento de Serviço e de Hubs de Eventos estão disponíveis para download e implantação. Clique nos links abaixo para obter detalhes sobre cada um, com links para os modelos no GitHub: 

* [Criar um namespace do Barramento de Serviço](service-bus-resource-manager-namespace.md)
* [Criar um namespace do Barramento de Serviço com fila](service-bus-resource-manager-namespace-queue.md)
* [Criar um namespace do Barramento de Serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
* [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## <a name="deploy-with-powershell"></a>Implantação com o PowerShell
O procedimento a seguir descreve como usar o PowerShell para implantar um modelo do Azure Resource Manager que cria um namespace de Barramento de Serviço de camada **Padrão** e uma fila dentro desse namespace. Este exemplo se baseia no modelo [Criar um namespace de Barramento de Serviço com fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue). O fluxo de trabalho é mais ou menos o seguinte:

1. Instale o PowerShell.
2. Crie o modelo e (opcionalmente) um arquivo de parâmetro.
3. No PowerShell, faça logon em sua conta do Azure.
4. Crie um novo grupo de recursos se já não tiver um.
5. Teste a implantação.
6. Se desejar, defina o modo de implantação.
7. Implante o modelo.

Para obter informações completas sobre a implantação de modelos do Azure Resource Manager, consulte [Implantar recursos com modelos do Azure Resource Manager][Implantar recursos com modelos do Azure Resource Manager].

### <a name="install-powershell"></a>Instalar o PowerShell
Siga as instruções em [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar o Azure PowerShell.

### <a name="create-a-template"></a>Criar um modelo
Clone ou copie o modelo [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) a partir do GitHub:

```
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
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-(optional)"></a>Criar um arquivo de parâmetros (opcional)
Para usar um arquivo de parâmetros opcionais, copie o arquivo [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json). Substitua o valor de `serviceBusNamespaceName` pelo nome do namespace do Barramento de Serviço que você deseja criar nessa implantação e substitua o valor do `serviceBusQueueName` pelo nome da fila que deseja criar. 

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Para saber mais, consulte o tópico [Arquivo de parâmetro](../resource-group-template-deploy.md#parameter-file).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Fazer logon no Azure e definir a assinatura do Azure
Em um prompt do PowerShell, execute o seguinte comando:

```
Login-AzureRmAccount
```

Você precisará entrar em sua conta do Azure. Após o logon, execute o comando a seguir para exibir as assinaturas disponíveis.

```
Get-AzureRMSubscription
```

Esse comando retorna uma lista de assinaturas do Azure disponíveis. Escolha uma assinatura para a sessão atual executando o comando a seguir. Substitua `<YourSubscriptionId>` pelo GUID da assinatura do Azure que deseja usar.

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Definir o grupo de recursos
Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos com o comando **New-AzureRmResourceGroup**. Forneça o nome do grupo de recursos e local que você deseja usar. Por exemplo:

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Se for bem-sucedido, um resumo do novo grupo de recursos será exibido.

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Teste a implantação
Valide a implantação executando o cmdlet `Test-AzureRmResourceGroupDeployment`. Ao testar a implantação, forneça parâmetros exatamente como faria durante a sua execução.

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Criar a implantação
Para criar a nova implantação, execute o comando `New-AzureRmResourceGroupDeployment` e forneça os parâmetros necessários quando solicitado. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos e o caminho ou a URL para o arquivo do modelo. Caso o parâmetro **Mode** não esteja especificado, o valor padrão de **Incremental** será usado. Para saber mais, consulte [Implantações incrementais e completas](../resource-group-template-deploy.md#incremental-and-complete-deployments).

O comando abaixo solicita os três parâmetros na janela do PowerShell:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Para especificar um arquivo de parâmetros em vez disso, use o comando a seguir.

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Você também pode usar parâmetros embutidos quando executa o cmdlet de implantação. O comando é o seguinte:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para executar uma implantação [completa](../resource-group-template-deploy.md#incremental-and-complete-deployments), defina o parâmetro **Mode** como **Complete**:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### <a name="verify-the-deployment"></a>Verificar a implantação
Se os recursos forem implantados com êxito, um resumo da implantação será exibido na janela do PowerShell:

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>Próximas etapas
Agora você já viu o fluxo de trabalho básico e os comandos para implantar um modelo do Azure Resource Manager. Para obter informações mais detalhadas, visite os seguintes links:

* [Visão geral do Azure Resource Manager][Visão geral do Azure Resource Manager]
* [Implantar recursos com modelos do Azure Resource Manager][Implantar recursos com modelos do Azure Resource Manager]
* [Criação de modelos](../resource-group-authoring-templates.md)

[Visão geral do Azure Resource Manager]: ../resource-group-overview.md
[Implantar recursos com modelos do Azure Resource Manager]: ../resource-group-template-deploy.md
[Galeria de Modelos de Início Rápido do Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus


<!--HONumber=Oct16_HO2-->


