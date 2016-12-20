---
title: "Modelo do Resource Manager para vinculação de recursos | Microsoft Docs"
description: "Mostra o esquema do Gerenciador de Recursos para implantar vínculos entre os recursos relacionados por meio de um modelo."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 48a13b1a-3208-4f91-ba85-bda2a0e22605
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: dcac45042b2511ae2b82ecf8278e9873c6f8c9b9


---
# <a name="resource-links-template-schema"></a>Vínculos de recursos - esquema do modelo
Cria um vínculo entre dois recursos. O vínculo é aplicado a um recurso conhecido como o recurso de origem. O segundo recurso no vínculo é conhecido como o recurso de destino.

## <a name="schema-format"></a>Formato de esquema
Para criar um vínculo, adicione o esquema a seguir à seção de recursos do seu modelo.

    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>Valores
As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Valor |
| --- | --- |
| type |Enum<br />Obrigatório<br />**{namespace}/{tipo}/providers/links**<br /><br />O tipo de recurso a ser criado. Os valores {namespace} e {tipo} referem-se ao tipo de recurso e ao namespace do provedor do recurso de origem. |
| apiVersion |Enum<br />Obrigatório<br />**01-01-2015**<br /><br />A versão da API a ser usada para criar o recurso. |
| Nome |Cadeia de caracteres<br />Obrigatório<br />**{recurso}/Microsoft.Resources/{nome do link}**<br /> até 64 caracteres e não pode conter <>, %, &, ? ou caracteres de controle.<br /><br />Um valor que especifica o nome do recurso da fonte e um nome para o vínculo. |
| dependsOn |Matriz<br />Opcional<br />Uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos.<br /><br />A coleção de recursos de que esse link depende. Se os recursos que estiver vinculando forem implantados no mesmo modelo, inclua esses nomes de recurso neste elemento para garantir que eles sejam implantados primeiro. |
| propriedades |Objeto<br />Obrigatório<br />[properties object](#properties)<br /><br />Um objeto que identifica o recurso para o qual vincular e observações sobre o vínculo. |

<a id="properties" />

### <a name="properties-object"></a>properties object
| Nome | Valor |
| --- | --- |
| targetId |Cadeia de caracteres<br />Obrigatório<br />**{ID do recurso}**<br /><br />O identificador do recurso alvo a ser vinculado. |
| HDInsight |Cadeia de caracteres<br />Opcional<br />até 512 caracteres<br /><br />Descrição do bloqueio. |

## <a name="how-to-use-the-link-resource"></a>Como usar o recurso de lin
Aplique um vínculo entre dois recursos quando os recursos tiverem uma dependência que continua após a implantação. Por exemplo, um aplicativo pode se conectar a um banco de dados em outro grupo de recursos. Você pode definir essa dependência criando um vínculo do aplicativo para o banco de dados. Os vínculos permitem que você documente a relação entre dois recursos. Posteriormente, você ou outra pessoa em sua organização pode consultar um recurso para obter vínculos para descobrir como o recurso funciona com outros recursos.

Todos os recursos vinculados devem pertencer à mesma assinatura. Cada recurso pode ser vinculado a 50 outros recursos. Se um dos recursos vinculados for excluído ou movido, o proprietário do vínculo deverá remover o vínculo restante.

Para trabalhar com vínculos por meio da REST, veja [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Use o comando do Azure PowerShell a seguir para ver todos os vínculos em sua assinatura. É possível fornecer outros parâmetros para limitar os resultados.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>Exemplos
O exemplo a seguir aplica um bloqueio somente leitura a um aplicativo Web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Modelos de início rápido
Os seguintes modelos de início rápido implantam recursos com um link.

* [Alerta de fila com Aplicativo lógico](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
* [Alerta de Margem de atraso com Aplicativo lógico](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
* [Provisionar um aplicativo de API com um gateway existente](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
* [Provisionar um aplicativo de API com um novo gateway](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
* [Crie um Aplicativo Lógico mais um aplicativo de API usando um modelo](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
* [Aplicativo lógico que envia uma mensagem de texto quando um alerta é acionado](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre a estrutura do modelo, veja [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).




<!--HONumber=Nov16_HO3-->


