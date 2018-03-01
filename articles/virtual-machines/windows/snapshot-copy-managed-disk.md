---
title: "Crie um instantâneo de um VHD no Azure | Microsoft Docs"
description: "Saiba como criar uma cópia de uma VM do Azure como um backup ou para solução de problemas."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: 9f773a8dfe772864fc9fc437052ac766a87623d1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-snapshot"></a>Criar um instantâneo

Capture um instantâneo de um VHD de disco de dados ou SO para backup ou solução de problemas de VM. Um instantâneo é uma cópia completa somente leitura de um VHD. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Usar o Portal do Azure para criar um instantâneo 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Iniciando no canto superior esquerdo, clique em **Criar um recurso** e procure **instantâneo**.
3. Na folha Instantâneo, clique em **Criar**.
4. Insira um **Nome** para o instantâneo.
5. Selecione um [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente ou digite o nome de um novo. 
6. Selecione um Local do datacenter do Azure.  
7. Para **Disco de origem**, selecione o Disco Gerenciado para instantâneo.
8. Escolha o **Tipo de conta** a ser usado para armazenar o instantâneo. É recomendável **Standard_LRS**, a menos que você precise dele armazenado em um disco de alto desempenho.
9. Clique em **Criar**.

## <a name="use-powershell-to-take-a-snapshot"></a>Usar o PowerShell para criar um instantâneo
As etapas a seguir mostram como obter o disco VHD a ser copiado, criar as configurações de instantâneo e capturar um instantâneo do disco usando o cmdlet [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Verifique se você tem a versão mais recente do módulo AzureRM.Compute do PowerShell instalada. Execute o comando a seguir para instalá-lo.

```
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
Para saber mais, confira [Azure PowerShell Versioning](/powershell/azure/overview) (Controle de versão do Azure PowerShell).


1. Defina alguns parâmetros. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. Obtenha o disco VHD a ser copiado.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Crie as configurações do instantâneo. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Crie o instantâneo.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Se você planeja usar o instantâneo para criar um Disco Gerenciado e anexá-lo a uma VM que precisa ser de alto desempenho, use o parâmetro `-AccountType Premium_LRS` com o comando New-AzureRmSnapshot. O parâmetro cria o instantâneo para que ele seja armazenado como um Disco Gerenciado Premium. Os Discos Gerenciados Premium são mais caros que os Standard. Portanto, assegure-se de que os discos Premium sejam realmente necessários antes de usar esse parâmetro.

## <a name="next-steps"></a>Próximas etapas

Cria uma máquina virtual usando um instantâneo criando um disco gerenciado do instantâneo e anexando o novo disco gerenciado como disco do SO. Para obter mais informações, consulte a amostra [Criar uma VM de um instantâneo](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
