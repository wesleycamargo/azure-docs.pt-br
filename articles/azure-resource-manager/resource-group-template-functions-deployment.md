---
title: "Funções de modelo do Azure Resource Manager – implantação | Microsoft Docs"
description: "Descreve as funções a serem usadas em um modelo do Resource Manager para recuperar informações sobre implantação."
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
ms.openlocfilehash: ce888415b6a5f82fb3d49834b055f8afe97442a8
ms.contentlocale: pt-br
ms.lasthandoff: 04/28/2017


---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Funções de implantação para modelos do Azure Resource Manager 

O Gerenciador de Recursos fornece as seguintes funções para obter os valores de seções do modelo e os valores relacionados à implantação:

* [implantação](#deployment)
* [parâmetros](#parameters)
* [variáveis](#variables)

Para obter valores de recursos, de grupos de recursos ou de assinaturas, veja [Funções de recurso](resource-group-template-functions-resource.md).

<a id="deployment" />

## <a name="deployment"></a>implantação
`deployment()`

Retorna informações sobre a operação de implantação atual.

### <a name="examples"></a>Exemplos

O exemplo a seguir retorna o objeto de implantação:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

O exemplo a seguir mostra como usar a implantação() para um modelo aninhado com base no URI do modelo pai.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

### <a name="return-value"></a>Valor de retorno

Essa função retorna o objeto que é passado durante a implantação. As propriedades no objeto retornado vão variar dependendo se o objeto de implantação for transmitido como um link ou como um objeto na linha. Quando o objeto de implantação é passado na linha, como ao usar o parâmetro **-TemplateFile** no Azure PowerShell para apontar para um arquivo local, o objeto retornado tem no seguinte formato:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quando o objeto é transmitido como um link, como ao usar o parâmetro **- TemplateUri** para apontar para um objeto remoto, o objeto é retornado no seguinte formato: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```



<a id="parameters" />

## <a name="parameters"></a>parâmetros
`parameters(parameterName)`

Retorna um valor de parâmetro. O nome do parâmetro especificado deve ser definido na seção de parâmetros do modelo.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |string |O nome do parâmetro a retornar. |

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra um uso simplificado da função parâmetros.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2014-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="return-value"></a>Valor de retorno

O tipo do parâmetro.

<a id="variables" />

## <a name="variables"></a>variáveis
`variables(variableName)`

Retorna o valor da variável. O nome do parâmetro especificado deve ser definido na seção variáveis do modelo.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| variableName |Sim |Cadeia de caracteres |O nome da variável a retornar. |

### <a name="examples"></a>Exemplos

O exemplo a seguir usa um valor variável.

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
],
```

### <a name="return-value"></a>Valor de retorno

O tipo da variável.

## <a name="next-steps"></a>Próximas etapas
* Para obter uma descrição das seções de um modelo do Azure Resource Manager, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar vários modelos, veja [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
* Para ver como implantar o modelo que você criou, veja [Implantar um aplicativo com o Modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).


