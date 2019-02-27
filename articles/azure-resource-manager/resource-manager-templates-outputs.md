---
title: Saídas do modelo do Azure Resource Manager | Microsoft Docs
description: Descreve como definir saídas para modelos do Azure Resource Manager usando a sintaxe JSON declarativa.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 92e5dd5190a76bd09e33ea4c40a5b5cc2d66bc7b
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301121"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Seção de saídas nos modelos do Azure Resource Manager

Na seção de saídas, você especifica valores que são retornados da implantação. Por exemplo, é possível retornar o URI para acessar um recurso implantado. Use a propriedade opcional `condition` para especificar se o valor de saída é retornado.

## <a name="define-and-use-output-values"></a>Definir e usar valores de saída

O exemplo a seguir mostra como retornar a ID de recurso para um endereço IP público:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

O próximo exemplo mostra como retornar condicionalmente a ID de recurso para um endereço IP público com base no fato de um novo ter sido implantado:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Após a implantação, você pode recuperar o valor com script. Para o PowerShell, use:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Para a CLI do Azure, use:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Você pode recuperar o valor de saída de um modelo vinculado usando a função [reference](resource-group-template-functions-resource.md#reference). Para obter um valor de saída de um modelo vinculado, recupere o valor da propriedade com uma sintaxe semelhante a: `"[reference('deploymentName').outputs.propertyName.value]"`.

Ao obter uma propriedade de saída de um modelo vinculado, o nome da propriedade não pode incluir um traço.

Por exemplo, você pode definir o endereço IP em um balanceador de carga recuperando um valor de um modelo vinculado.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Não é possível usar a `reference` função na seção de saídas de um [modelo aninhado](resource-group-linked-templates.md#link-or-nest-a-template). Para retornar os valores de um recurso implantado em um modelo aninhado, converta seu modelo aninhado em um modelo vinculado.

## <a name="available-properties"></a>Propriedades disponíveis

O exemplo a seguir mostra a estrutura de uma definição de saída:

```json
"outputs": {
    "<outputName>" : {
        "condition": "<boolean-value-whether-to-output-value>",
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nome do elemento | Obrigatório | DESCRIÇÃO |
|:--- |:--- |:--- |
| outputName |Sim |Nome do valor de saída. Deve ser um identificador JavaScript válido. |
| condition |Não  | Valor booliano que indica se esse valor de saída é retornado. Quando `true`, o valor é incluído na saída para a implantação. Quando `false`, o valor de saída é ignorado para esta implantação. Quando não especificado, o valor padrão é `true`. |
| Tipo |Sim |Tipo do valor de saída. Valores de saída oferecem suporte aos mesmos tipos que os parâmetros de entrada do modelo. |
| value |Sim |Expressão de linguagem do modelo avaliada e retornada como valor de saída. |

Para obter informações sobre a inclusão de comentários, consulte [Comentários em modelos](resource-group-authoring-templates.md#comments).

## <a name="example-templates"></a>Modelos de exemplo

|Modelo  |DESCRIÇÃO  |
|---------|---------|
|[Variáveis de cópia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Cria variáveis complexas e gera os valores. Ele não implanta nenhum recurso. |
|[Endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Cria um endereço IP público e gera a ID de recurso. |
|[Balanceador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Links para o modelo anterior. Usa a ID do recurso na saída ao criar o balanceador de carga. |


## <a name="next-steps"></a>Próximas etapas
* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que podem ser usadas em um modelo, consulte [Funções do Modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para obter recomendações sobre como criar modelos, consulte [Melhores práticas para modelos do Azure Resource Manager](template-best-practices.md).
