---
title: "Criar aplicativos lógicos dos modelos do Azure Resource Manager | Microsoft Docs"
description: "Criar e implantar fluxos de trabalho de aplicativo lógico com modelos do Azure Resource Manager"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; mandia
ms.openlocfilehash: e30ed8b1b8e2241bbebab1d7c5f337fabf37e1dd
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2017
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Criar e implantar aplicativos lógicos com modelos do Azure Resource Manager

Os Aplicativos Lógicos do Azure fornecem modelos do Azure Resource Manager que você pode usar não apenas para criar aplicativos lógicos para automatizar os fluxos de trabalho, como também para definir os recursos e parâmetros utilizados para implantação. Você pode usar esse modelo para seus próprios cenários de negócios ou personalize o modelo para atender às suas necessidades. Saiba mais sobre o [modelo do Resource Manager para aplicativos lógicos](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) e sobre a [estrutura e a sintaxe de modelo do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Definir o aplicativo lógico

Esta definição de aplicativo lógico de exemplo é executada uma vez por hora e executa ping no local especificado no parâmetro `testUri`.
O modelo usa valores de parâmetro para o nome do aplicativo lógico (```logicAppName```) e o local para executar o ping para teste (```testUri```). Saiba mais sobre a [definição destes parâmetros no seu modelo](#define-parameters). O modelo também define o local do aplicativo lógico como o mesmo local do grupo de recursos do Azure. 

``` json
{
    "type": "Microsoft.Logic/workflows",
    "apiVersion": "2016-06-01",
    "name": "[parameters('logicAppName')]",
    "location": "[resourceGroup().location]",
    "tags": {
        "displayName": "LogicApp"
    },
    "properties": {
        "definition": {
            "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "testURI": {
                "type": "string",
                "defaultValue": "[parameters('testUri')]"
                }
            },
            "triggers": {
                "Recurrence": {
                    "type": "Recurrence",
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            },
            "actions": {
                "Http": {
                    "type": "Http",
                    "inputs": {
                        "method": "GET",
                        "uri": "@parameters('testUri')"
                    },
                    "runAfter": {}
                }
            },
            "outputs": {}
        },
        "parameters": {}
    }
}
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>Definir parâmetros

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Veja as descrições para os parâmetros do modelo:

| Parâmetro | Descrição | Exemplo de definição JSON | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Define o nome do aplicativo lógico criado pelo modelo. | "nomedoAplicativoLógico": { "type": "string", "metadados": { "descrição": "nomedoMeuAplicativoLógicoDeExemplo" } } |
| `testUri` | Define o local para executar o ping para teste. | "testUri": { "type": "string", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

Saiba mais sobre a [API REST para definição e propriedades do Fluxo de Trabalho de Aplicativos Lógicos](https://docs.microsoft.com/rest/api/logic/workflows) e sobre a [criação de definições de aplicativo lógico com JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Implantar aplicativos lógicos automaticamente

Para criar e implantar automaticamente um aplicativo lógico para o Azure, escolha **Implantar no Azure** aqui:

[![Implantar no Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Essa ação conectará você ao portal do Azure, onde você poderá fornecer os detalhes do seu aplicativo lógico e fazer alterações no modelo ou nos parâmetros. Por exemplo, o portal do Azure solicitará estes detalhes:

* Nome da assinatura do Azure
* Grupo de recursos que você deseja usar
* Local do aplicativo lógico
* Um nome para o seu aplicativo lógico
* Um URI de teste
* Aceitação dos termos e das condições especificados

## <a name="deploy-logic-apps-with-commands"></a>Implantar aplicativos lógicos com comandos

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>CLI do Azure

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Monitorar aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md)