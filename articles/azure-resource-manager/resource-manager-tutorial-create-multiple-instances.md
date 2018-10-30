---
title: Criar várias instâncias de recursos usando o Azure Resource Manager | Microsoft Docs
description: Saiba como criar um modelo do Azure Resource Manager para criar várias instâncias de recurso do Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/18/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1cfccdf644b1748a96f7638e574c66eace8d113a
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456653"
---
# <a name="tutorial-create-multiple-resource-instances-using-resource-manager-templates"></a>Tutorial: Criar várias instâncias de recursos usando modelos do Resource Manager

Saiba como iterar em seu modelo do Azure Resource Manager para criar várias instâncias de um recurso do Azure. No último tutorial, você modificou um modelo existente para criar uma conta criptografada de Armazenamento do Azure. Neste tutorial, você modificará o mesmo modelo para criar três instâncias de conta de armazenamento.

> [!div class="checklist"]
> * Abrir um modelo de início rápido
> * Editar o modelo
> * Implantar o modelo

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a [extensão de Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

O modelo usado neste início rápido é chamado [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso da conta de Armazenamento do Azure.

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.
4. Escolha **Arquivo**>**Salvar como** para salvar o arquivo como **azuredeploy.json** em seu computador local.

## <a name="edit-the-template"></a>Editar o modelo

O objetivo deste tutorial é usar a iteração de recursos para criar três contas de armazenamento.  O modelo de exemplo apenas cria uma conta de armazenamento. 

No Visual Studio Code, faça as quatro alterações a seguir:

![O Azure Resource Manager cria várias instâncias](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Adicione um elemento `copy` à definição de recurso de conta de armazenamento. No elemento de cópia, você deve especificar o número de iterações e um nome para esse loop. O valor da contagem deve ser um número inteiro positivo e não pode exceder 800.
2. A função `copyIndex()` retorna a iteração atual no loop. `copyIndex()`é baseado em zero. Para deslocar o valor do índice, você pode passar um valor na função copyIndex(). Por exemplo, *copyIndex(1)*.
3. Exclua o elemento **variables**, porque ele não é mais usado.
4. Exclua o elemento **outputs**.

O modelo concluído se parece com:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Para saber mais sobre como criar várias instâncias, consulte [Implantar várias instâncias de um recurso ou propriedade em modelos do Azure Resource Manager](./resource-group-create-multiple.md)

## <a name="deploy-the-template"></a>Implantar o modelo

Consulte a seção [Implantar o modelo](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) do guia de início rápido do Visual Studio Code para o procedimento de implantação.

Para listar todas as três contas de armazenamento, omita o parâmetro --name:

# <a name="clitabcli"></a>[CLI](#tab/CLI)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName
```

---

Compare os nomes de conta de armazenamento com a definição de nome no modelo.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar várias instâncias da conta de armazenamento. Até agora, você criou uma conta de armazenamento ou várias instâncias de conta de armazenamento. No próximo tutorial, você desenvolverá um modelo com vários recursos e vários tipos de recursos. Alguns dos recursos têm recursos dependentes.

> [!div class="nextstepaction"]
> [Criar recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
