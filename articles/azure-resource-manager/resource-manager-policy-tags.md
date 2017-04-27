---
title: "Políticas de recurso do Azure para marcas | Microsoft Docs"
description: "Fornece exemplos de políticas de recurso para gerenciamento de marcas em recursos"
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
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 6e71fd9eda822478fa0555aa44908a4094fe8de2
ms.lasthandoff: 03/31/2017


---
# <a name="apply-resource-policies-for-tags"></a>Aplicar políticas de recurso a marcas

Este tópico fornece regras de política comuns que podem ser aplicadas para garantir o uso consistente de marcas em recursos.

A aplicação de uma política de marca a um grupo de recursos ou a uma assinatura com recursos existentes não aplica retroativamente a política a esses recursos. Para impor as políticas a esses recursos, dispare uma atualização para os recursos existentes, conforme mostrado em [Trigger updates to existing resources](#trigger-updates-to-existing-resources) (Disparar atualizações para recursos existentes).

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Garantir que todos os recursos em um grupo de recursos tenham um valor/marca

Um requisito comum é o de que todos os recursos em um grupo de recursos tenham uma marca e um valor específicos. Esse requisito geralmente é necessário para controlar custos por departamento. As seguintes condições devem ser atendidas:

* A marca e o valor necessários são acrescentados a recursos novos e atualizados que não têm marcas existentes.
* A marca e o valor necessários são acrescentados a recursos novos e atualizados que têm outras marcas, mas não a marca e o valor necessários.
* A marca e o valor necessários não podem ser removidos de nenhum recurso existente.

Você pode atender a esse requisito aplicando a um grupo de recursos as três seguintes políticas:

* [Acrescentar marca](#append-tag) 
* [Acrescentar marca com outras marcas](#append-tag-with-other-tags)
* [Exigir marca e valor](#require-tag-and-value)

### <a name="append-tag"></a>Acrescentar marca

A seguinte regra de política acrescentará a marca costCenter com um valor predefinido quando nenhuma marca estiver presente:

```json
{
  "if": {
    "field": "tags",
    "exists": "false"
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags",
        "value": {"costCenter":"myDepartment" }
      }
    ]
  }
}
```

### <a name="append-tag-with-other-tags"></a>Acrescentar marca com outras marcas

A seguinte regra de política acrescentará a marca costCenter com um valor predefinido quando marcas estiverem presentes, mas a marca costCenter não estiver definida:

```json
{
  "if": {
    "allOf": [
      {
        "field": "tags",
        "exists": "true"
      },
      {
        "field": "tags.costCenter",
        "exists": "false"
      }
    ]
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags.costCenter",
        "value": "myDepartment"
      }
    ]
  }
}
```

### <a name="require-tag-and-value"></a>Exigir marca e valor

A regra de política a seguir nega a atualização ou criação de recursos que não têm a marca costCenter atribuída ao valor predefinido.

```json
{
  "if": {
    "not": {
      "field": "tags.costCenter",
      "equals": "myDepartment"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="require-tags-for-a-resource-type"></a>Exigir marcas para um tipo de recurso
O exemplo a seguir mostra como aninhar operadores lógicos para exigir uma marca de aplicativo somente para um tipo de recurso especificado (nesse caso, contas de armazenamento).

```json
{
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
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>Exigir marca
A seguinte política nega as solicitações que não têm uma marca contendo a chave "costCenter" (qualquer valor pode ser aplicado):

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="trigger-updates-to-existing-resources"></a>Disparar atualizações para recursos existentes

O script do PowerShell a seguir dispara uma atualização para recursos existentes de modo a impor políticas de marca que você adicionou.

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($_.Tags -eq $NULL) { @{}} else {$_.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
* Depois de definir uma regra de política (conforme mostrado nos exemplos anteriores), você precisará criar a definição de política e atribuí-la a um escopo. O escopo pode ser uma assinatura, grupo de recursos ou recurso. Para atribuir políticas por meio do portal, consulte [Usar o portal do Azure para atribuir e gerenciar políticas de recurso](resource-manager-policy-portal.md). Para atribuir políticas por meio da API REST, do PowerShell ou da CLI do Azure, consulte [Atribuir e gerenciar políticas por meio de script](resource-manager-policy-create-assign.md).
* Para ver uma introdução às políticas de recurso, confira [Visão geral da política de recurso](resource-manager-policy.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).


