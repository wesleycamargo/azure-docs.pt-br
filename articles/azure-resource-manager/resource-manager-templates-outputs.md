---
title: "Saídas do modelo do Azure Resource Manager | Microsoft Docs"
description: "Descreve como definir saídas para modelos do Azure Resource Manager usando a sintaxe JSON declarativa."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: 485a3eb5c5d04d1540482245d088c48645704465
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Seção de saídas nos modelos do Azure Resource Manager
Na seção de saídas, você especifica valores que são retornados da implantação. Por exemplo, é possível retornar o URI para acessar um recurso implantado.

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

Após a implantação, você pode recuperar o valor com script. Para o PowerShell, use:

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Para a CLI do Azure, use:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Você pode recuperar o valor de saída de um modelo vinculado usando a função [reference](resource-group-template-functions-resource.md#reference). Para obter um valor de saída de um modelo vinculado, recupere o valor da propriedade com uma sintaxe semelhante a: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Por exemplo, você pode definir o endereço IP em um balanceador de carga recuperando um valor de um modelo vinculado.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

## <a name="available-properties"></a>Propriedades disponíveis

O exemplo a seguir mostra a estrutura de uma definição de saída:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nome do elemento | Obrigatório | DESCRIÇÃO |
|:--- |:--- |:--- |
| outputName |sim |Nome do valor de saída. Deve ser um identificador JavaScript válido. |
| Tipo |sim |Tipo do valor de saída. Valores de saída oferecem suporte aos mesmos tipos que os parâmetros de entrada do modelo. |
| value |sim |Expressão de linguagem do modelo avaliada e retornada como valor de saída. |

## <a name="recommendations"></a>Recomendações

Se você usar um modelo para criar endereços IP públicos, inclua uma seção outputs que retorne detalhes do endereço IP e o FQDN (nome de domínio totalmente qualificado). É possível usar valores de saída para recuperar facilmente detalhes sobre endereços IP públicos e FQDNs após a implantação.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>Modelos de exemplo


|Modelo  |DESCRIÇÃO  |
|---------|---------|
|[Variáveis de cópia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Cria variáveis complexas e gera os valores. Ele não implanta nenhum recurso. |
|[Endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Cria um endereço IP público e gera a ID de recurso. |
|[Balanceador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Links para o modelo anterior. Usa a ID do recurso na saída ao criar o balanceador de carga. |


## <a name="next-steps"></a>Próximas etapas
* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que podem ser usadas em um modelo, consulte [Funções do Modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para combinar vários modelos durante a implantação, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Talvez seja necessário usar recursos que existam em um grupo de recursos diferente. Essa situação é comum ao trabalhar com contas de armazenamento ou redes virtuais compartilhadas com vários grupos de recursos. Para obter mais informações, consulte a [função resourceId](resource-group-template-functions-resource.md#resourceid).