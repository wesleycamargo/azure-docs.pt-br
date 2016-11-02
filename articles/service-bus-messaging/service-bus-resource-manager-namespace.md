<properties
    pageTitle="Criar um namespace do Barramento de Serviço usando um modelo do Resource Manager | Microsoft Azure"
    description="Usar modelo do Azure Resource Manager para criar um namespace do Barramento de Serviço"
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
    ms.date="10/04/2016"
    ms.author="sethm;shvija"/>


# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Criar um namespace do Barramento de Serviço usando um modelo do Azure Resource Manager

Este artigo descreve como usar um modelo do Azure Resource Manager que cria um namespace de Barramento de Serviço do tipo **Mensagens** com um SKU Standard/Básico. O artigo também define os parâmetros que são especificados para execução da implantação. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades.

Para saber mais sobre a criação de modelos, confira [Criando modelos do Azure Resource Manager][].

Para ver o modelo completo, consulte o [Modelo de namespace do Barramento de Serviço][] no GitHub.

>[AZURE.NOTE] Os modelos do Azure Resource Manager a seguir estão disponíveis para download e implantação. 
>
>-    [Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Criar um namespace do Barramento de Serviço com fila](service-bus-resource-manager-namespace-queue.md)
>-    [Criar um namespace do Barramento de Serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
>-    [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](service-bus-resource-manager-namespace-auth-rule.md)
>
>Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][] e procure por Barramento de Serviço.

## <a name="what-will-you-deploy?"></a>O que você implantará?

Com esse modelo, você implantará um namespace de Barramento de Serviço com uma SKU [Básica, Standard ou Premium](https://azure.microsoft.com/pricing/details/service-bus/).

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` , que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

Este modelo define os parâmetros a seguir.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

O nome do namespace do Barramento de Serviço a ser criado.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU

O nome do [SKU](https://azure.microsoft.com/pricing/details/service-bus/) do Barramento de Serviço a ser criado.

```
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

```

O modelo definirá os valores permitidos para esse parâmetro (Basic, Standard ou Premium) e atribuirá um valor padrão (Standard) se nenhum valor for especificado.

Para saber mais sobre os preços do Barramento de Serviço, consulte [Barramento de Serviço, preços e cobrança][].

### <a name="servicebusapiversion"></a>serviceBusApiVersion

A versão da API do Barramento de Serviço do modelo.

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Recursos a implantar

### <a name="service-bus-namespace"></a>Namespace do Barramento de Serviço

Cria um namespace do Barramento de Serviço padrão do tipo **Mensagens**.

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>CLI do Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou e implantou recursos usando o Azure Resource Manager, saiba como gerenciar esses recursos lendo estes artigos:

- [Gerenciar o Barramento de Serviço com o PowerShell](service-bus-powershell-how-to-provision.md)
- [Gerenciar recursos do Barramento de Serviço com o Service Bus Explorer](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Criando modelos do Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Modelo de namespace do Barramento de Serviço]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Modelos de Início Rápido do Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Barramento de Serviço, preços e cobrança]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Usando o Azure PowerShell com o Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure]: ../xplat-cli-azure-resource-manager.md



<!--HONumber=Oct16_HO2-->


