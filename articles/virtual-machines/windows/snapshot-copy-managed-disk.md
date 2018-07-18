---
title: Crie um instantâneo de um VHD no Azure | Microsoft Docs
description: Saiba como criar uma cópia de uma VM do Azure como um backup ou para solução de problemas.
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: d7315d3fb7fc156beb85271d0e5aa19ec6baa7a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
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

Antes de iniciar, confira se você tem a versão mais recente do módulo AzureRM.Compute do PowerShell. Este artigo requer o módulo do AzureRM versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

Defina alguns parâmetros. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

Obtenha a VM.

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

Crie a configuração do instantâneo. Neste exemplo, vamos obter um instantâneo do disco do sistema operacional.

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> Se você quiser armazenar o instantâneo no armazenamento com resiliência de zona, será necessário criá-lo em uma região que dê suporte para [zonas de disponibilidade](../../availability-zones/az-overview.md) e inclua o parâmetro `-SkuName Standard_ZRS`.   

   
Crie o instantâneo.

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>Próximas etapas

Cria uma máquina virtual usando um instantâneo criando um disco gerenciado do instantâneo e anexando o novo disco gerenciado como disco do SO. Para obter mais informações, consulte a amostra [Criar uma VM de um instantâneo](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
