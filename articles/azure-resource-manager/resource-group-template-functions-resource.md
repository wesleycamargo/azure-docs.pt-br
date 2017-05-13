---
title: "Funções de modelo do Azure Resource Manager – recursos | Microsoft Docs"
description: "Descreve as funções a serem usadas em um modelo do Azure Resource Manager para recuperar valores sobre recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 66c71906614e5d0c8e8531271444fc59a5cb779f
ms.contentlocale: pt-br
ms.lasthandoff: 04/28/2017


---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Funções de recursos para modelos do Azure Resource Manager

O Gerenciador de Recursos fornece as seguintes funções para obter valores de recurso:

* [listKeys e list{Value}](#listkeys)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)

Para obter valores de parâmetros, de variáveis ou da implantação atual, veja [Funções de valor de implantação](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="listkeys-and-listvalue"></a>listKeys e list{Value}
`listKeys(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

Retorna os valores para qualquer tipo de recurso que ofereça suporte à operação de lista. O uso mais comum é `listKeys`. 

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| resourceName ou resourceIdentifier |Sim |string |Identificador exclusivo para o recurso. |
| apiVersion |Sim |string |Versão de API do estado de tempo de execução do recurso. Normalmente, no formato **aaaa-mm-dd**. |

### <a name="remarks"></a>Comentários

Qualquer operação que começa com **list** pode ser usada como uma função no seu modelo. As operações disponíveis não incluem apenas listKeys, mas também operações como `list`, `listAdminKeys` e `listStatus`. Para determinar quais tipos de recursos têm uma operação de lista, use as seguintes opções:

* Veja as [operações da API REST](/rest/api/) de um provedor de recursos e procure por operações de lista. Por exemplo, as contas de armazenamento têm a [operação listKeys](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Use o cmdlet do PowerShell [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation). O exemplo a seguir obtém todas as operações de lista de contas de armazenamento:

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Use o seguinte comando da CLI do Azure e o utilitário JSON [jq](http://stedolan.github.io/jq/download/) para filtrar apenas as operações de lista:

  ```azurecli
  azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"
  ```

Especifique o recurso usando a [função resourceId](#resourceid) ou o formato `{providerNamespace}/{resourceType}/{resourceName}`.

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra como retornar as chaves primárias e secundárias de uma conta de armazenamento na seção de saídas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountId": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "storageKeysOutput": {
            "value": "[listKeys(parameters('storageAccountId'), '2016-01-01')]",
            "type" : "object"
        }
    }
}
``` 

### <a name="return-value"></a>Valor de retorno

O objeto retornado de listKeys tem o seguinte formato:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Outras funções de lista têm formatos diferentes de retorno. Para ver o formato de uma função, inclua-a na seção de saídas, conforme mostra o exemplo de modelo. 

<a id="providers" />

## <a name="providers"></a>providers
`providers(providerNamespace, [resourceType])`

Retorna informações sobre um provedor de recursos e seus tipos de recursos com suporte. Se você não fornecer um tipo de recurso, a função retornará todos os tipos com suporte para o provedor de recursos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| providerNamespace |Sim |string |Namespace do provedor |
| resourceType |Não |string |O tipo de recurso no namespace especificado. |

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra como usar a função provider:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

Cada tipo com suporte é retornado no seguinte formato: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

A ordenação de matriz dos valores retornados não é garantida.

<a id="reference" />

## <a name="reference"></a>reference
`reference(resourceName or resourceIdentifier, [apiVersion])`

Retorna um objeto que representa o estado de tempo de execução de um recurso.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| resourceName ou resourceIdentifier |Sim |string |Nome ou identificador exclusivo de um recurso. |
| apiVersion |Não |string |Versão da API do recurso especificado. Inclua esse parâmetro quando o recurso não estiver provisionado no mesmo modelo. Normalmente, no formato **aaaa-mm-dd**. |

### <a name="remarks"></a>Comentários

A função de referência deriva seu valor de um estado de tempo de execução e, portanto, não pode ser usada na seção de variáveis. Ela pode ser usada na seção de saídas de um modelo. 

Usando a função de referência, você declara implicitamente que um recurso depende de outro recurso se o recurso referenciado é provisionado no mesmo modelo. Você não precisa usar a propriedade dependsOn também. A função não é avaliada até que o recurso referenciado conclua a implantação.

Para ver os valores e nomes de propriedade para um tipo de recurso, crie um modelo que retorne o objeto na seção de saídas . Se você tiver um recurso existente desse tipo, o modelo retornará o objeto sem implantar qualquer recurso. 

### <a name="examples"></a>Exemplos

O exemplo a seguir faz referência a uma conta de armazenamento que não foi implantada no modelo, mas existe dentro do mesmo grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }
}
```

Ou, você pode implantar e fazer referência ao recurso no mesmo modelo.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      }
    }
}
``` 

Normalmente, você usa a função de referência para retornar um valor específico de um objeto, como o URI do ponto de extremidade de blob ou o nome de domínio totalmente qualificado.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

### <a name="return-value"></a>Valor de retorno

Cada tipo de recurso retorna propriedades diferentes para a função de referência. A função não retorna um único formato predefinido. Para ver as propriedades de um tipo de recurso, retorne o objeto na seção de saída, conforme mostra o exemplo.

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Retorna um objeto que representa o grupo de recursos atual. 

### <a name="examples"></a>Exemplos

O modelo a seguir retorna as propriedades do grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Um uso comum da função resourceGroup é criar recursos no mesmo local que o grupo de recursos. O exemplo a seguir usa o local do grupo de recursos para atribuir o local de um site Web.

```json
"resources": [
   {
      "apiVersion": "2014-06-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="return-value"></a>Valor de retorno

O objeto retornado está no seguinte formato:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Retorna o identificador exclusivo de um recurso. Você pode usar essa função quando o nome do recurso é ambíguo ou não provisionado no mesmo modelo. 

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| subscriptionId |Não |string (no formato GUID) |O valor padrão é a assinatura atual. Especifique esse valor quando você precisar recuperar um recurso em outra assinatura. |
| resourceGroupName |Não |string |O valor padrão é o grupo de recursos atual. Especifique esse valor quando você precisar recuperar um recurso em outro grupo de recursos. |
| resourceType |Sim |string |Tipo de recurso, incluindo o namespace do provedor de recursos. |
| resourceName1 |Sim |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome do recurso se o recurso está aninhado. |

### <a name="examples"></a>Exemplos

O exemplo a seguir retorna a ID de recurso de uma conta de armazenamento no grupo de recursos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceIdOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        }
    }
}
```

O exemplo a seguir mostra como recuperar as IDs de recurso de um site em outro grupo de recursos, e um banco de dados em um grupo de recursos diferente:

```json
[resourceId('otherResourceGroup', 'Microsoft.Web/sites', parameters('siteName'))]
[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
```

Frequentemente, você precisa usar essa função ao usar uma conta de armazenamento ou rede virtual em um grupo de recursos alternativo. A conta de armazenamento ou a rede virtual pode ser usada em vários grupos de recursos; portanto, você não deve excluí-los ao excluir um único grupo de recursos. O exemplo a seguir mostra como um recurso de um grupo de recursos externo pode ser facilmente usado:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="return-value"></a>Valor de retorno

O identificador é retornado no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

<a id="subscription" />

## <a name="subscription"></a>subscription
`subscription()`

Retorna detalhes sobre a assinatura da implantação atual. 

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra a função de assinatura chamada na seção de saídas. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

### <a name="return-value"></a>Valor de retorno

A função retorna o seguinte formato:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma descrição das seções de um modelo do Azure Resource Manager, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar vários modelos, veja [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
* Para ver como implantar o modelo que você criou, veja [Implantar um aplicativo com o modelo do Azure Resource Manager](resource-group-template-deploy.md).


