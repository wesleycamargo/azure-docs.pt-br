---
title: Criar um namespace do Barramento de Serviço com tópico e assinatura usando um modelo do Azure Resource Manager | Microsoft Docs
description: Criar um namespace do Barramento de Serviço com tópico e assinatura usando um modelo do Azure Resource Manager
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
ms.author: sethm;shvija

---
# <a name="create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Criar um namespace do Barramento de Serviço com tópico e assinatura usando um modelo do Azure Resource Manager
Este artigo mostra como usar um modelo do Azure Resource Manager que cria um namespace do Barramento de Serviço com tópico e assinatura. Você aprenderá como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades

Para saber mais sobre a criação de modelos, confira [Criando modelos do Azure Resource Manager][Criando modelos do Azure Resource Manager].

Para ver o modelo completo, consulte o [Modelo de namespace do Barramento de Serviço com tópico e assinatura][Modelo de namespace do Barramento de Serviço com tópico e assinatura] no GitHub.

> [!NOTE]
> Os modelos do Azure Resource Manager a seguir estão disponíveis para download e implantação.
> 
> * [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um namespace do Barramento de Serviço com fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um namespace do Barramento de Serviço](service-bus-resource-manager-namespace.md)
> * [Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
> 
> Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][Modelos de Início Rápido do Azure] e procure por Barramento de Serviço.
> 
> 

## <a name="what-will-you-deploy?"></a>O que você implantará?
Com este modelo, você implantará um namespace de Barramento de Serviço com tópico e assinatura.

Os [tópicos e as assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) fornecem uma forma de comunicação de um para muitos, em um padrão de *publicação/assinatura*.

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros
Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` , que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
O nome do namespace do Barramento de Serviço a ser criado.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
O nome do tópico criado no namespace do Barramento de Serviço.

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
O nome da assinatura criada no namespace do Barramento de Serviço.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
A versão da API do Barramento de Serviço do modelo.

```
"serviceBusApiVersion": {
"type": "string"
}
```
## <a name="resources-to-deploy"></a>Recursos a implantar
Cria um namespace de Barramento de Serviço padrão do tipo **Mensagens**, com tópico e assinatura.

```
"resources ": [{
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
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implantação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Próximas etapas
Agora que você criou e implantou recursos usando o Azure Resource Manager, saiba como gerenciar esses recursos consultando estes artigos:

* [Gerenciar o Barramento de Serviço com o PowerShell](../service-bus/service-bus-powershell-how-to-provision.md)
* [Gerenciar recursos do Barramento de Serviço com o Service Bus Explorer](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[Criando modelos do Azure Resource Manager]: ../resource-group-authoring-templates.md
[Modelos de Início Rápido do Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Tópicos e assinaturas do Barramento de Serviço]: service-bus-queues-topics-subscriptions.md
[Usando o Azure PowerShell com o Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure]: ../xplat-cli-azure-resource-manager.md
[Modelo de namespace do Barramento de Serviço com tópico e assinatura]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/



<!--HONumber=Oct16_HO2-->


