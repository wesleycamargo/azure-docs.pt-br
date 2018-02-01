---
title: "Localizar e excluir discos gerenciados e não gerenciados do Azure desconectados | Microsoft Docs"
description: "Como localizar e excluir discos gerenciados e não gerenciados (VHDs/Blobs de páginas) do Azure desconectados, usando a CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 9ada768cd4128b9dd6949b5a96c557496c6bb11c
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Localizar e excluir discos gerenciados e não gerenciados do Azure desconectados
Quando você exclui uma máquina virtual no Azure, os discos conectados a ela não são excluídos por padrão. Isso evita a perda de dados devido a máquinas virtuais excluídas por engano, mas você continua pagando desnecessariamente os discos desconectados. Use este artigo para localizar e excluir todos os discos desconectados e reduzir os custos. 


## <a name="find-and-delete-unattached-managed-disks"></a>Localizar e excluir discos gerenciados desconectados 

O script a seguir mostra como localizar os Discos Gerenciados desconectados, utilizando a propriedade ManagedBy.  Ele executa um loop de todos os discos gerenciados em uma assinatura e verifica se a propriedade *ManagedBy* está nula para localizar os discos gerenciados desconectados. A propriedade *ManagedBy* armazena a ID do recurso da máquina virtual para qual um disco gerenciado está conectado. 

É altamente recomendável que você primeiro execute o script, configurando a variável *deleteUnattachedDisks* como 0 para exibir todos os discos desconectados. Após revisar os discos desconectados, execute o script, configurando *deleteUnattachedDisks* como 1 para excluir todos os discos desconectados.

 ```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done

```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Localizar e excluir discos não gerenciados desconectados 

Discos não gerenciados são arquivos VHD armazenados como [blobs de páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) nas [Contas de Armazenamento do Microsoft Azure](../../storage/common/storage-create-storage-account.md). O script a seguir mostra como localizar discos não gerenciados desconectados (blobs de páginas) usando a propriedade LeaseStatus. Ele executa um loop de todos os discos não gerenciados em todas as contas de armazenamento em uma assinatura e verifica se a propriedade *LeaseStatus* está desbloqueada para localizar discos não gerenciados desconectados. *LeaseStatus* estará configurada como bloqueada, se um disco não gerenciado estiver conectado a uma máquina virtual. 

É altamente recomendável que você primeiro execute o script, configurando a variável *deleteUnattachedVHDs* como 0 para exibir todos os discos desconectados. Após revisar os discos desconectados, execute o script, configurando *deleteUnattachedVHDs* como 1 para excluir todos os discos desconectados.


 ```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 

```

## <a name="next-steps"></a>Próximas etapas

[Excluir conta de armazenamento](../../storage/common/storage-create-storage-account.md)



