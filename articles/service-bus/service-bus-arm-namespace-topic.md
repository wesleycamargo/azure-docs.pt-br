<properties
    pageTitle="Criar um namespace do Barramento de Serviço com tópico e assinatura | Microsoft Azure"
    description="Criar um namespace do Barramento de Serviço com tópico e assinatura usando modelo do ARM"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# Criar um namespace do Barramento de Serviço com tópico e assinatura usando um modelo do ARM

Este artigo mostra como usar um modelo do ARM (Azure Resource Manager) que cria um namespace do Barramento de Serviço com um tópico e uma assinatura. Você aprenderá como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [Criando modelos do Azure Resource Manager][].

Para ver o modelo completo, consulte o [Modelo de namespace do Barramento de Serviço com tópico e assinatura][] no GitHub.

>[AZURE.NOTE] Os seguintes modelos do ARM estão disponíveis para download e implantação.
>
>-    [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](service-bus-arm-namespace-auth-rule.md)
>-    [Criar um namespace do Barramento de Serviço com um Hub de Eventos e grupo de consumo](service-bus-arm-namespace-event-hub.md)
>-    [Criar um namespace do Barramento de Serviço com fila](service-bus-arm-namespace-queue.md)
>-    [Criar um namespace do Barramento de Serviço](service-bus-arm-namespace.md)
>
>Para verificar os modelos mais recentes, consulte [Modelos de início rápido do Azure][] e procure por Barramento de Serviço.

## O que você implantará?

Com este modelo, você implantará um namespace de Barramento de Serviço com tópico e assinatura.

Tópicos e assinaturas fornecem uma forma de comunicação de um para muitos, em um padrão de *publicação/assinatura*.

Saiba mais sobre [Tópicos e assinaturas do Barramento de Serviço][].

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/service-bus-arm-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## Parâmetros

Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters`, que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

Descreveremos cada parâmetro no modelo.

### serviceBusNamespaceName

O nome do namespace do Barramento de Serviço a ser criado.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### serviceBusTopicName

O nome do tópico criado no namespace do Barramento de Serviço.

```
"serviceBusTopicName": {
"type": "string"
}
```

### serviceBusSubscriptionName

O nome da assinatura criada no namespace do Barramento de Serviço.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```

### serviceBusApiVersion

A versão da API do Barramento de Serviço do modelo.

```
" serviceBusApiVersion": {
"type": "string"
}
```
## Recursos a implantar

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

## Comandos para executar a implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## CLI do Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## Próximas etapas

Agora que você criou e implantou recursos usando o ARM, aprenda a gerenciar esses recursos consultando estes artigos:

- [Gerenciar Barramento de Serviço do Azure usando a Automação do Azure](service-bus-automation-manage.md)
- [Gerenciar o Barramento de Serviço com o PowerShell](service-bus-powershell-how-to-provision.md)
- [Gerenciar recursos do Barramento de Serviço com o Service Bus Explorer](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Criando modelos do Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Modelos de início rápido do Azure]: https://azure.microsoft.com/documentation/templates/
  [Tópicos e assinaturas do Barramento de Serviço]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Modelo de namespace do Barramento de Serviço com tópico e assinatura]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/

<!---HONumber=AcomDC_0420_2016-->