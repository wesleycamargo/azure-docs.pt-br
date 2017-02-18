---
title: "Vincular recursos relacionados em soluções do Azure | Microsoft Docs"
description: Crie um link entre os recursos relacionados em diferentes grupos de recursos no Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0738d072-c093-4cf1-a790-de13025d9d60
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 2750e971ae281e498e9c505e1cd2790cefbfbc78


---
# <a name="link-related-resources-from-different-resource-groups"></a>Vincular recursos relacionados de diferentes grupos de recursos
Durante a implantação, você pode marcar um recurso como dependente de outro recurso, mas esse ciclo de vida termina na implantação. Após a conclusão da implantação, não há nenhuma relação identificada entre os recursos dependentes. O Azure Resource Manager oferece um recurso chamado vinculação de recursos para estabelecer relações entre os recursos.

Com a vinculação de recursos, você pode documentar as relações que se estendem pelos grupos de recursos. Por exemplo, é comum colocar um banco de dados com seu próprio ciclo de vida em um grupo de recursos e um aplicativo com um ciclo de vida diferente em outro grupo de recursos. O aplicativo se conecta ao banco de dados e, portanto, marque um link entre o aplicativo e o banco de dados. 

Todos os recursos vinculados devem pertencer à mesma assinatura. Cada recurso pode ser vinculado a 50 outros recursos. A única maneira de consultar os recursos relacionados é por meio da API REST. Se um dos recursos vinculados for excluído ou movido, o proprietário do vínculo deverá remover o vínculo restante. Você **não** será avisado quando excluir um recurso que esteja vinculado a outros recursos.

## <a name="linking-in-templates"></a>Vinculando em modelos
Para definir um link em um modelo, inclua um tipo de recurso que combine o namespace do provedor de recursos e o tipo de recurso de origem com **/providers/links**. O nome deve incluir o nome do recurso de origem. Forneça a ID do recurso de destino. O exemplo a seguir estabelece um link entre um site e uma conta de armazenamento.

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


Para obter uma descrição completa do formato de modelo, confira [Links de recursos - esquema de modelo](resource-manager-template-links.md).

## <a name="linking-with-rest-api"></a>Vinculando com a API REST
Para definir um vínculo entre recursos implantados, execute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Substitua {subscription-id} pelo ID da assinatura. Substitua {resource-group}, {provider-namespace}, {resource-type} e {resource-name} pelos valores que identificam o primeiro recurso no vínculo. Substitua {link-name} pelo nome do vínculo a ser criado. Use 2015-01-01 para a versão de API.

Na solicitação, inclua um objeto que define o segundo recurso no vínculo:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

O elemento properties contém o identificador do segundo recurso.

Você pode consultar os links de consulta em sua assinatura com:

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Para obter mais exemplos, incluindo como recuperar informações sobre vínculos, consulte [Recursos vinculados](https://docs.microsoft.com/rest/api/resources/resourcelinks).

## <a name="next-steps"></a>Próximas etapas
* Você também pode organizar seus recursos com marcas. Para saber mais sobre marcação de recursos, consulte [Usando marcas para organizar os recursos](resource-group-using-tags.md).
* Para obter uma descrição de como criar modelos e definir os recursos a serem implantados, consulte [Criando modelos](resource-group-authoring-templates.md).




<!--HONumber=Jan17_HO4-->


