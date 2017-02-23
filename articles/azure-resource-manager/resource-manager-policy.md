---
title: "Políticas de recurso do Azure | Microsoft Docs"
description: "Descreve como usar as políticas do Azure Resource Manager para garantir recursos consistentes propriedades são definidas durante a implantação. As políticas podem ser aplicadas em grupos de recursos ou de assinatura."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 6d459e37b8b39f5d76c4ec86ebb7351c783b81fb
ms.openlocfilehash: 64cb4be184e02519a6c496f8639035201ebb60f8


---
# <a name="resource-policy-overview"></a>Visão geral de políticas de recursos
Políticas de recursos permitem que você estabeleça convenções para recursos em sua organização. Definindo as convenções, você pode controlar os custos e muito mais fácil gerenciar seus recursos. Por exemplo, você pode especificar que somente determinados tipos de máquinas virtuais são permitidos, ou você pode exigir que todos os recursos tenham uma determinada marca. As políticas são herdadas por todos os recursos filho. Então, se uma política for aplicada a um grupo de recursos, ela será aplicável a todos os recursos desse grupo de recursos.

Há dois conceitos a entender sobre políticas:

* definição da política - descrevem quando a política é aplicada e qual ação tomar
* atribuição de política - aplicar a definição de política a um escopo (assinatura ou grupo de recursos)

Este tópico se concentra na definição de política. Para saber mais sobre a atribuição de política, veja [Atribuir e gerenciar políticas](resource-manager-policy-create-assign.md).

O Azure fornece algumas definições de política internas que podem reduzir o número de políticas que você precisa definir. Se uma definição de política interna funciona para seu cenário, use essa definição ao atribuir a um escopo.

Políticas são avaliadas durante a criação e atualização de recursos (PUT e operações de PATCHES).

> [!NOTE]
> Atualmente, a política não avalia os tipos de recursos que não dão suporte a marcas, tipo e local, como o tipo de recurso Microsoft.Resources/deployments. Esse suporte será adicionado no futuro. Para evitar problemas de compatibilidade com versões anteriores, você deve especificar explicitamente o tipo ao criar políticas. Por exemplo, uma política de marcação que não especifica tipos é aplicada a todos os tipos. Nesse caso, uma implantação de modelo poderá falhar se houver um recurso aninhado que não dê suporte a marcas e o tipo de recurso de implantação tiver sido adicionado à avaliação da política. 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>Qual é a diferença dela em relação ao RBAC?
Há algumas diferenças importantes entre a política e o RBAC (controle de acesso baseado em função). O RBAC se concentra nas ações do **usuário** em escopos diferentes. Por exemplo, você é adicionado à função de colaborador de um grupo de recursos no escopo do desejado, para que você possa fazer alterações a esse grupo de recursos. Diretiva enfoca **recursos** propriedades durante a implantação. Por exemplo, por meio de políticas, é possível controlar os tipos de recursos que podem ser provisionados ou restringir os locais em que os recursos podem ser provisionados. Ao contrário do RBAC, a política é um sistema de permissão padrão e negação explícita. 

Para usar políticas, você deve estar autenticado pelo RBAC. Especificamente, a conta precisa de:

* `Microsoft.Authorization/policydefinitions/write` permissão para definir uma política
* `Microsoft.Authorization/policyassignments/write` permissão para atribuir uma política 

Essas permissões não estão incluídas na função **Colaborador**.

## <a name="policy-definition-structure"></a>Estrutura da definição de política
Você usa JSON para criar uma definição de política. A definição de política contém elementos para:

* parameters
* nome de exibição
* description
* regra de política
  * avaliação de lógica
  * efeito

O exemplo a seguir mostra uma política que limita os locais em que os recursos são implantados:

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## <a name="parameters"></a>Parâmetros
O uso de parâmetros ajuda a simplificar o gerenciamento de política, reduzindo o número de definições de política. Defina uma política para uma propriedade de recurso (por exemplo, limitando os locais onde os recursos podem ser implantados) e incluir parâmetros na definição. Em seguida, reutilizar essa definição de política para diferentes cenários pela passagem de valores diferentes (como especificar um conjunto de locais para uma assinatura) quando atribuir a política.

Declare parâmetros ao criar definições de política.

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

O tipo de um parâmetro pode ser cadeia de caracteres ou matriz. A propriedade de metadados é usada para que ferramentas como o portal do Azure exibam informações amigáveis ao usuário. 

Na regra de política, você fazer referência a parâmetros com a seguinte sintaxe: 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Nome de exibição e descrição

Você usa o **displayName** e **description** para identificar a definição de política e fornecer contexto para quando ele é usado.

## <a name="policy-rule"></a>Regra de política

A regra de política consiste em **se** e **, em seguida,** blocos. No **se** bloco, você define uma ou mais condições que especificam quando a política é aplicada. Você pode aplicar os operadores lógicos para essas condições para definir exatamente o cenário para uma política. No **, em seguida,** bloco, você define o efeito que acontecerá quando o **se** condições sejam atendidas.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>Operadores lógicos
Os operadores lógicos com suporte são:

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

O **não** sintaxe inverte o resultado da condição. A sintaxe de **allOf** (semelhante à operação **E** lógica) requer que todas as condições sejam verdadeiras. A sintaxe de **anyOf** (semelhante à operação **Ou** lógica) requer que uma ou mais condições sejam verdadeiras.

Você pode aninhar operadores lógicos. A exemplo a seguir mostra uma operação **Não** operação é aninhada dentro de uma operação **E**. 

```json
"if": {
  "allOf": [
    {
      "not": {
        "field": "tags",
        "containsKey": "application"
      }
    },
    {
      "field": "type",
      "equals": "Microsoft.Storage/storageAccounts"
    }
  ]
},
```

### <a name="conditions"></a>Conditions
Uma condição avalia se um **campo** atende a determinados critérios. As condições com suporte são:

* `"equals": "value"`
* `"like": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Ao usar a condição **like**, você pode fornecer um curinga (*) no valor.

### <a name="fields"></a>Campos
As condições são formadas usando campos. Um campo representa as propriedades na carga de solicitação de recurso que é usada para descrever o estado do recurso.  

Há suporte para os seguintes campos:

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* aliases de propriedade

Você pode usar aliases de propriedade para acessar propriedades específicas para um tipo de recurso. Os aliases com suporte são:

* Microsoft.CDN/profiles/sku.name
* Microsoft.Compute/virtualMachines/imageOffer
* Microsoft.Compute/virtualMachines/imagePublisher
* Microsoft.Compute/virtualMachines/sku.name
* Microsoft.Compute/virtualMachines/imageSku 
* Microsoft.Compute/virtualMachines/imageVersion
* Microsoft.SQL/servers/databases/edition
* Microsoft.SQL/servers/databases/elasticPoolName
* Microsoft.SQL/servers/databases/requestedServiceObjectiveId
* Microsoft.SQL/servers/databases/requestedServiceObjectiveName
* Microsoft.SQL/servers/elasticPools/dtu
* Microsoft.SQL/servers/elasticPools/edition
* Microsoft.SQL/servers/version
* Microsoft.Storage/storageAccounts/accessTier
* Microsoft.Storage/storageAccounts/enableBlobEncryption
* Microsoft.Storage/storageAccounts/sku.name
* Microsoft.Web/serverFarms/sku.name

### <a name="effect"></a>Efeito
A política dá suporte a três tipos de efeito - `deny`, `audit` e `append`. 

* **Negar** gera um evento no log de auditoria e causa uma falha da solicitação
* **Auditar** gera um evento de aviso no log de auditoria, mas não causa falha da solicitação
* **Acrescentar** adiciona o conjunto de campos definido à solicitação 

Para **acrescentar**, você precisa fornecer os detalhes abaixo:

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

O valor pode ser uma cadeia de caracteres ou um objeto no formato JSON. 

## <a name="policy-examples"></a>Exemplos de políticas

Os tópicos a seguir contêm exemplos de política:

* Para obter exemplos de políticas de marca, veja [Aplicar políticas de recursos para marcas](resource-manager-policy-tags.md).
* Para obter exemplos de políticas de armazenamento, veja [Aplicar políticas de recursos para contas de armazenamento](resource-manager-policy-storage.md).
* Para obter exemplos de políticas de máquina virtual, veja [Aplicar políticas de recursos a VMs Linux](../virtual-machines/virtual-machines-linux-policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) e [Aplicar políticas de recursos a VMs do Windows](../virtual-machines/virtual-machines-windows-policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

### <a name="allowed-resource-locations"></a>Locais de recursos permitidos
Para especificar quais locais são permitidos, veja o exemplo da seção [Estrutura da definição de política](#policy-definition-structure). Para atribuir essa definição de política, use a política interna com a ID de recurso `/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c`.

### <a name="not-allowed-resource-locations"></a>Não permitido locais de recursos
Para especificar quais locais não são permitidas, use a seguinte definição de política:

```json
{
  "properties": {
    "parameters": {
      "notAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that are not allowed when deploying resources",
          "strongType": "location",
          "displayName": "Not allowed locations"
        }
      }
    },
    "displayName": "Not allowed locations",
    "description": "This policy enables you to block locations that your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "field": "location",
        "in": "[parameters('notAllowedLocations')]"
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="allowed-resource-types"></a>Tipos de recursos permitidos
O exemplo a seguir mostra uma política que permite implantações somente nos tipos de recurso Microsoft.Resources, Microsoft.Compute, Microsoft.Storage, Microsoft.Network. Todos os outros são negados:

```json
{
  "if": {
    "not": {
      "anyOf": [
        {
          "field": "type",
          "like": "Microsoft.Resources/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Compute/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Storage/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Network/*"
        }
      ]
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

### <a name="set-naming-convention"></a>Definir a convenção de nomenclatura
O exemplo a seguir mostra o uso de curingas, que é compatível com a condição **like**. A condição declara que, se o nome corresponder ao padrão mencionado (namePrefix\*nameSuffix), a solicitação será negada:

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Próximas etapas
* Depois de definir uma regra de política, atribua um escopo. Para saber mais sobre a atribuição de política, veja [Atribuir e gerenciar políticas](resource-manager-policy-create-assign.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).
* O esquema da política é publicado em [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 




<!--HONumber=Feb17_HO3-->


