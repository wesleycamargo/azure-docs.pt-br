---
title: "Políticas de Azure Resource Manager para armazenamento | Microsoft Docs"
description: "Descreve políticas do Azure Resource Manager para gerenciar a implantação de contas de armazenamento."
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
ms.date: 01/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: ce31bbcb96a6afe19cf6c25dd9a68d6c2d4d080c
ms.openlocfilehash: f2141304f6db6d137065e06558e10c721b11f892


---
# <a name="apply-azure-resource-policies-to-storage-accounts"></a>Aplicar políticas de recursos do Azure para contas de armazenamento
Por meio de políticas do Azure Resource Manager, você define regras consistentes para como os recursos são implantados na sua organização. Criar políticas personalizadas para garantir que os usuários em sua organização não quebrem as convenções necessárias para gerenciar recursos da sua organização. Este tópico mostra várias políticas que definem regras para Contas de Armazenamento do Azure. Para saber mais sobre políticas, veja [Usar políticas de recursos para gerenciar recursos](resource-manager-policy.md).

Os exemplos neste tópico mostram valores embutidos na regra de política. No entanto, você pode usar parâmetros para passar valores que são usados ao atribuir a política. Para saber mais, veja [Parâmetros da política](resource-manager-policy.md#parameters).

## <a name="define-permitted-storage-account-types"></a>Definir tipos de conta de armazenamento permitidos

A seguinte política restringe quais [tipos de conta de armazenamento](../storage/storage-redundancy.md) podem ser implantados:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="define-permitted-access-tier"></a>Definir o nível de acesso permitido

A política a seguir especifica o tipo de [camada de acesso](../storage/storage-blob-storage-tiers.md) que pode ser especificado para contas de armazenamento:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>Certifique-se de que a criptografia esteja habilitada

A seguinte política requer que todas as contas de armazenamento para habilitar [criptografia do serviço de armazenamento](../storage/storage-service-encryption.md):

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="create-and-assign-policies"></a>Criar e atribuir políticas

Depois de definir uma regra de política (conforme mostrado nos exemplos anteriores), você precisará criar a política e atribuí-la a um escopo. O escopo pode ser uma assinatura, grupo de recursos ou recurso. Para obter exemplos sobre a criação e a atribuição de políticas, veja [Criar e atribuir uma política](resource-manager-policy.md#create-and-assign-a-policy). 

## <a name="next-steps"></a>Próximas etapas
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).




<!--HONumber=Jan17_HO3-->


