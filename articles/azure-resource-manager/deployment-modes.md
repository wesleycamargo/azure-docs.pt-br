---
title: Modos de implantação do Azure Resource Manager | Microsoft Docs
description: Descreve como especificar se um modo de implantação completo ou incremental deve ser usado com o Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: c4347254df59c62085b2bfb195496bf479cf7b35
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344564"
---
# <a name="azure-resource-manager-deployment-modes"></a>Modos de implantação do Azure Resource Manager

Ao implantar os recursos, especifique que a implantação é uma atualização incremental ou uma atualização completa.  A principal diferença entre esses dois modos é como o Gerenciador de Recursos lida com recursos existentes no grupo de recursos que não estão no modelo. O modo padrão é incremental.

## <a name="incremental-and-complete-deployments"></a>Implantações incrementais e completas

Ao implantar recursos:

* No modo completo, o Gerenciador de recursos **exclui** recursos existentes no grupo de recursos, mas que não são especificados no modelo.
* No modo incremental, o Gerenciador de recursos **deixa inalterados** recursos existentes no grupo de recursos, mas que não são especificados no modelo.

Para ambos os modos, o Resource Manager tenta criar todos os recursos especificados no modelo. Se o recurso já existe no grupo de recursos e suas configurações são as mesmas, a operação resulta em nenhuma alteração. Se você alterar os valores de propriedade de um recurso, o recurso será atualizado com os novos valores. Se você tentar atualizar o local ou o tipo de um recurso existente, a implantação falhará com um erro. Em vez disso, implante um novo recurso com o local ou o tipo de que você precisa.

Ao reimplantar um recurso no modo incremental, especifique todos os valores de propriedade para o recurso, não apenas aqueles que você está atualizando. Se você não especificar certas propriedades, o Resource Manager interpretará que a atualização está substituindo esses valores.

## <a name="example-result"></a>Resultados de exemplo

Para ilustrar a diferença entre os modos incrementais e completos, considere o cenário a seguir.

O **Grupo de recursos** contém:

* Recurso A
* Recurso B
* Recurso C

O **Modelo** contém:

* Recurso A
* Recurso B
* Recurso D

Quando implantada no **incremental** modo, o grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso C
* Recurso D

Quando implantado no modo **completo**, o Recurso C é excluído. O grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso D

## <a name="set-deployment-mode"></a>Definir o modo de implantação

Para definir o modo de implantação ao implantar com o PowerShell, use o `Mode` parâmetro.

```azurepowershell-interactive
New-AzureRmResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Para definir o modo de implantação ao implantar com a CLI do Azure, use o `mode` parâmetro.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Ao usar um modelo [vinculado ou aninhado](resource-group-linked-templates.md), você deve definir a `mode` propriedade como `Incremental`. Somente modelos de nível raiz suporte ao modo de implantação completa.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de modelos do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
* Para saber mais sobre como implantar recursos, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).
* Para exibir as operações para um provedor de recursos, consulte [API REST do Azure](/rest/api/).
