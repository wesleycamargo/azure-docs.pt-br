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
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# Criar um namespace do Barramento de Serviço usando um modelo do Azure Resource Manager

Este artigo mostra como usar um modelo do Azure Resource Manager que cria um namespace de “Mensagens” do Barramento de Serviço com um SKU Standard/Básico. O artigo também define os parâmetros que são especificados para execução da implantação. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades.

Para obter mais informações sobre a criação de modelos, consulte [Criando modelos do Azure Resource Manager][].

Para ver o modelo completo, consulte o [Modelo de namespace do Barramento de Serviço][] no GitHub.

>[AZURE.NOTE] Os modelos do Azure Resource Manager a seguir estão disponíveis para download e implantação.
>
>-    [Criar um namespace do Barramento de Serviço com um Hub de Eventos e grupo de consumo](service-bus-resource-manager-namespace-event-hub.md)
>-    [Criar um namespace do Barramento de Serviço com fila](service-bus-resource-manager-namespace-queue.md)
>-    [Criar um namespace do Barramento de Serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
>-    [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](service-bus-resource-manager-namespace-auth-rule.md)
>
>Para verificar os modelos mais recentes, consulte [Modelos de início rápido do Azure][] e procure por Barramento de Serviço.

## O que você implantará?

Com este modelo, você implantará um namespace do Barramento de Serviço com um SKU [Básico ou Standard](https://azure.microsoft.com/pricing/details/service-bus/).

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## Parâmetros

Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters`, que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

Descreveremos cada parâmetro no modelo.

### serviceBusNamespaceName

O nome do namespace do Barramento de Serviço a ser criado.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### serviceBusSKU

O nome do [SKU](https://azure.microsoft.com/pricing/details/service-bus/) do Barramento de Serviço a ser criado.

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

O modelo definirá os valores que são permitidos para esse parâmetro (Básico ou Standard) e atribuirá um valor padrão (Standard) se nenhum valor for especificado.

Há um encargo de base da camada Standard de US$ 10 por mês, que permite que você execute até 12,5 milhões de operações por mês sem custos adicionais. A camada Básica custa US$ 0,05 por milhão de operações.

Para obter mais informações sobre os preços do Barramento de Serviço, consulte [Barramento de Serviço, preços e cobrança][].

### serviceBusApiVersion

A versão da API do Barramento de Serviço do modelo.

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## Recursos a implantar

### Namespace do Barramento de Serviço

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

## Comandos para executar a implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### CLI do Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## Próximas etapas

Agora que você criou e implantou recursos usando o Azure Resource Manager, saiba como gerenciar esses recursos consultando estes artigos:

- [Gerenciar Barramento de Serviço do Azure usando a Automação do Azure](service-bus-automation-manage.md)
- [Gerenciar o Barramento de Serviço com o PowerShell](service-bus-powershell-how-to-provision.md)
- [Gerenciar recursos do Barramento de Serviço com o Service Bus Explorer](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Criando modelos do Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Modelo de namespace do Barramento de Serviço]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Modelos de início rápido do Azure]: https://azure.microsoft.com/documentation/templates/
  [Barramento de Serviço, preços e cobrança]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0518_2016-->