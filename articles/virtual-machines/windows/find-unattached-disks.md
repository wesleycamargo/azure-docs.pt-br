---
title: "Localizar e excluir discos gerenciados e não gerenciados do Azure desconectados | Microsoft Docs"
description: "Como localizar e excluir discos gerenciados e não gerenciados (VHDs/Blobs de páginas) do Azure desconectados, usando o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Localizar e excluir discos gerenciados e não gerenciados do Azure desconectados
Quando você exclui uma máquina virtual no Azure, os discos conectados a ela não são excluídos por padrão. Isso evita a perda de dados devido a máquinas virtuais excluídas por engano, mas você continua pagando desnecessariamente os discos desconectados. Use este artigo para localizar e excluir todos os discos desconectados e reduzir os custos. 


## <a name="find-and-delete-unattached-managed-disks"></a>Localizar e excluir discos gerenciados desconectados 

O script a seguir mostra como localizar os [Discos Gerenciados](managed-disks-overview.md) desconectados, utilizando a propriedade *ManagedBy*. Ele executa um loop de todos os discos gerenciados em uma assinatura e verifica se a propriedade *ManagedBy* está nula para localizar os discos gerenciados desconectados. A propriedade *ManagedBy* armazena a ID do recurso da máquina virtual para qual um disco gerenciado está conectado.

É altamente recomendável que você primeiro execute o script, configurando a variável *deleteUnattachedDisks* como 0 para exibir todos os discos desconectados. Após revisar os discos desconectados, execute o script, configurando *deleteUnattachedDisks* como 1 para excluir todos os discos desconectados.

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Localizar e excluir discos não gerenciados desconectados 

Discos não gerenciados são arquivos VHD armazenados como [blobs de páginas] (/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) nas [Contas de Armazenamento do Microsoft Azure](../../storage/common/storage-create-storage-account.md). O script a seguir mostra como localizar discos não gerenciados (blobs de páginas) desconectados usando a propriedade *LeaseStatus*. Ele executa um loop de todos os discos não gerenciados em todas as contas de armazenamento em uma assinatura e verifica se a propriedade *LeaseStatus* está desbloqueada para localizar discos não gerenciados desconectados. *LeaseStatus* estará configurada como bloqueada, se um disco não gerenciado estiver conectado a uma máquina virtual.

É altamente recomendável que você primeiro execute o script, configurando a variável *deleteUnattachedVHDs* como 0 para exibir todos os discos desconectados. Após revisar os discos desconectados, execute o script, configurando *deleteUnattachedVHDs* como 1 para excluir todos os discos desconectados.


```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}

```

## <a name="next-steps"></a>Próximas etapas

[Excluir conta de armazenamento](../../storage/common/storage-create-storage-account.md)




