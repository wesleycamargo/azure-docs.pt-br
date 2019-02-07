---
title: Mover recursos do Azure para outro grupo de recursos | Microsoft Docs
description: Saiba como mover recursos do Azure de um grupo de recursos para outro ou de uma assinatura para outra.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/19/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3aadeb92fccc2baa445bce73e3d3111168aeecf6
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490238"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group-or-subscription"></a>Tutorial: Mover recursos do Azure para outro grupo de recursos ou assinatura

Saiba como mover recursos do Azure de um grupo de recursos para outro. Você também pode mover recursos do Azure de uma assinatura do Azure para outra. Neste tutorial, você pode usar um modelo do Resource Manager para implantar dois grupos de recursos e uma conta de armazenamento. Em seguida, mova a conta de armazenamento de um grupo de recursos para outro.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Prepare os recursos.
> * Verifique se que o recurso pode ser movido.
> * Lista de verificação antes de mover o recurso.
> * Valide a operação de movimentação.
> * Mova o recurso.
> * Limpe os recursos.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prepare-the-resources"></a>Preparar os recursos

Um modelo foi criado e colocado em uma [conta de armazenamento compartilhada](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json). O modelo define dois grupos de recursos e uma conta de armazenamento. Ao implantar o modelo, você precisa fornecer um nome de projeto. O nome do projeto é usado para gerar nomes de recurso exclusivos.  O JSON a seguir é extraído do modelo:

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

Observe os locais definidos em JSON, os dois grupos de recursos estão localizados no Leste dos EUA e no Oeste dos EUA. A conta de armazenamento está localizada no Leste dos EUA. Quando você move um recurso para outro grupo de recursos com uma localização diferente, a operação de movimentação não altera a localização do recurso.

Selecione **Experimentar** para abrir o Cloud Shell e, em seguida, execute o script do PowerShell no Cloud Shell:

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

Aguarde até o script ser concluído com êxito e, em seguida, abra o [portal do Azure](https://portal.azure.com) e verifique se os grupos de recursos e a conta de armazenamento são implantados como esperado.

> [!NOTE]
> Uma vez que o modelo define dois grupos de recursos, essa implantação é considerada uma implantação no nível da assinatura. A implantação de modelo do portal não oferece suporte a implantações no nível de assinatura. Assim, o Azure PowerShell é usado neste tutorial. A CLI do Azure também é compatível com implantações no nível de assinatura. Confira [Criar grupos de recursos e recursos par uma assinatura do Azure](./deploy-to-subscription.md).

## <a name="verify-the-resource-can-be-moved"></a>Verifique se o recurso pode ser movido

Nem todos os recursos podem ser movidos. O recurso usado neste tutorial é uma conta de armazenamento, que pode ser movida. Para verificar se um recurso pode ser movido, confira [Serviços que podem ser movidos](./resource-group-move-resources.md#services-that-can-be-moved).

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos

Esta etapa é opcional para este tutorial, como foi feito.

Há algumas etapas importantes a serem executadas antes de mover um recurso. Confira a [Lista de verificação antes de mover recursos](./resource-group-move-resources.md#checklist-before-moving-resources).

## <a name="validate-the-move"></a>Validar a movimentação

Validar a movimentação é opcional para este tutorial, como foi feito.

A operação de validar movimentação permite que você teste seu cenário de movimentação sem realmente mover os recursos. Use essa operação para determinar se a movimentação será bem-sucedida. Para obter mais informações, confira [Validar a movimentação](./resource-group-move-resources.md#validate-move).

## <a name="move-the-resource"></a>Mover o recurso

A conta de armazenamento está dentro do grupo de recursos de origem (rg1), executar o seguinte script do PowerShell move o recurso para o grupo de recursos de destino (rg2). Use o mesmo nome de projeto usado ao implantar os recursos.

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

Abra o [portal do Azure](https://portal.azure.com), verifique se a conta de armazenamento foi movida para outro grupo de recursos e verifique também se a localização da conta de armazenamento ainda é Leste dos EUA.

O grupo de origem e o grupo de destino ficam bloqueados durante a operação de movimentação de recursos. As operações de gravação e exclusão são bloqueadas nos grupos de recursos até que a migração seja concluída. Esse bloqueio significa que você não pode adicionar, atualizar nem excluir os recursos nos grupos de recursos, mas isso não significa que os recursos estão congelados. Por exemplo, se você mover um SQL Server e seu banco de dados para um novo grupo de recursos, um aplicativo que usa o banco de dados não terá nenhuma inatividade. Ele ainda poderá ler e gravar no banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos de origem.  
4. Escolha **Excluir grupo de recursos** no menu superior.
5. Selecione o nome de grupo de recursos de destino.  
6. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a mover uma conta de armazenamento de um grupo de recursos para outro. Até agora, você lidou com uma conta de armazenamento ou várias instâncias de conta de armazenamento. No próximo tutorial, você desenvolverá um modelo com vários recursos e vários tipos de recursos. Alguns dos recursos têm recursos dependentes.

> [!div class="nextstepaction"]
> [Criar recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
