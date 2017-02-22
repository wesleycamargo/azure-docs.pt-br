---
title: Modelo do Resource Manager para bloqueios do recurso | Microsoft Docs
description: "Mostra o esquema do Gerenciador de Recursos para a implantação de bloqueios de recursos por meio de um modelo."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: de6c0c57-e33a-4960-98e0-900901592003
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 16f87b8859a5d98c2328388ef12b13ef0e261861


---
# <a name="resource-locks-template-schema"></a>Esquema do modelo de bloqueios de recursos
Cria um bloqueio em um recurso e seus recursos-filho.

## <a name="schema-format"></a>Formato de esquema
Para criar um bloqueio, adicione o esquema a seguir à seção de recursos do seu modelo.

    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>Valores
As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Obrigatório | Descrição |
| --- | --- | --- |
| type |Sim |O tipo de recurso a ser criado.<br /><br />Para os recursos:<br />**{namespace}/{tipo}/providers/locks**<br /><br/>Para os grupos de recursos:<br />**Microsoft.Authorization/locks** |
| apiVersion |Sim |A versão da API a ser usada para criar o recurso.<br /><br />Use:<br />**01-01-2015**<br /><br /> |
| name |sim |Um valor que especifica o recurso para bloquear e um nome para o bloqueio. Pode ter até 64 caracteres e não pode conter <, > %, &, ? ou caracteres de controle.<br /><br />Para os recursos:<br />**{recurso}/Microsoft.Authorization/{nomebloqueio}**<br /><br />Para os grupos de recursos:<br />**{nomebloqueio}** |
| dependsOn |Não |Uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos.<br /><br />A coleção de recursos de que esse bloqueio depende. Se o recurso que você está bloqueando estiver implantado no mesmo modelo, inclua o nome desse recurso neste elemento para garantir que o recurso seja implantado primeiro. |
| propriedades |sim |Um objeto que identifica o tipo de bloqueio, bem como observações sobre o bloqueio.<br /><br />Consulte as [propriedades do objeto](#properties-object). |

### <a name="properties-object"></a>properties object
| Nome | Obrigatório | Descrição |
| --- | --- | --- |
| level |sim |O tipo de bloqueio a ser aplicado ao escopo.<br /><br />**CannotDelete**: os usuários podem modificar o recurso, mas não excluí-lo.<br />**ReadOnly**: os usuários podem ler um recurso, mas não podem excluí-lo nem executar ações nele. |
| HDInsight |Não |Descrição do bloqueio. Pode ter até 512 caracteres. |

## <a name="how-to-use-the-lock-resource"></a>Como usar o recurso de bloqueio
Esse recurso é adicionado ao seu modelo para impedir as ações especificadas em um recurso. O bloqueio se aplica a todos os usuários e grupos.

Para criar ou excluir os bloqueios de gerenciamento, você deve ter acesso às ações **Microsoft.Authorization/*** ou **Microsoft.Authorization/locks/***. Das funções internas, apenas **Proprietário** e **Administrador de Acesso do Usuário** recebem essas ações. Para saber mais sobre o controle de acesso baseado em funções, consulte [Controle de Acesso baseado em funções do Azure](../active-directory/role-based-access-control-configure.md).

O bloqueio é aplicado ao recurso especificado e a todos os recursos filho.

É possível remover um bloqueio com o comando **Remove-AzureRmResourceLock** do PowerShell ou com a [operação de exclusão](https://msdn.microsoft.com/library/azure/mt204562.aspx) da API REST.

## <a name="examples"></a>Exemplos
O exemplo a seguir aplica um bloqueio contra exclusão a um aplicativo Web.

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
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

O próximo exemplo aplica um bloqueio contra exclusão ao grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre a estrutura do modelo, veja [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
* Para obter mais informações sobre bloqueios, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md).




<!--HONumber=Nov16_HO3-->


