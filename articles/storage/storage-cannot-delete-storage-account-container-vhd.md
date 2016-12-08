---
title: "Solucionar problemas ao excluir contas de armazenamento, contêineres ou VHDs do Azure em uma implantação clássica | Microsoft Docs"
description: "Solucionar problemas ao excluir contas de armazenamento, contêineres ou VHDs do Azure em uma implantação clássica"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: tysonn
tags: storage
ms.assetid: 0f7a8243-d8dc-432a-9d37-1272a0cb3a5c
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a50ac3b7fe76dd44887fb197f685c1311d9dc04c


---
# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Solucionar problemas ao excluir contas de armazenamento, contêineres ou VHDs do Azure em uma implantação clássica
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Você pode receber erros ao tentar excluir a conta de armazenamento, o contêiner ou o VHD do Azure no [portal do Azure](https://portal.azure.com/) ou no [portal clássico do Azure](https://manage.windowsazure.com/). Os problemas podem ser causados pelas seguintes circunstâncias:

* Ao excluir uma VM, o disco e o VHD não são automaticamente excluídos. Esse pode ser o motivo da falha na exclusão da conta de armazenamento. Não excluímos o disco para que você possa usá-lo para montar outra VM.
* Ainda há uma concessão em um disco ou o blob que está associado ao disco.

Se o problema do Azure não for resolvido neste artigo, visite os fóruns do Azure no [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou usando @AzureSupport no Twitter. Além disso, você pode registrar uma solicitação de suporte do Azure selecionando **Obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Sintomas
A seção a seguir lista erros comuns que você pode receber ao tentar excluir contas de armazenamento, contêineres ou VHDs do Azure.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Cenário 1: Não é possível excluir uma conta de armazenamento
Ao navegar para a conta de armazenamento no [portal do Azure](https://portal.azure.com/) ou [portal clássico do Azure](https://manage.windowsazure.com/) e selecionar **Excluir**, você pode ver a seguinte mensagem de erro:

*A conta de armazenamento StorageAccountName contém imagens de VM. Assegure-se de que essas imagens de VM sejam removidas antes de excluir essa conta de armazenamento.*

Você também poderá ver este erro:

**No portal do Azure**:

*Falha ao excluir a conta de armazenamento <vm-storage-account-name>. Não é possível excluir a conta de armazenamento <vm-storage-account-name>: a conta de armazenamento <vm-storage-account-name> tem algumas imagens e/ou discos ativos. Garanta que esses discos e/ou essas imagens sejam removidos antes de excluir essa conta de armazenamento.'*.

**No Portal Clássico do Azure**:

*A conta de armazenamento <vm-storage-account-name> tem algumas imagens e/ou discos ativos, por exemplo, xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Garanta que esses discos e/ou essas imagens sejam removidos antes de excluir essa conta de armazenamento.*

ou o

**No portal do Azure**:

*A conta de armazenamento <vm-storage-account-name> tem um contêiner com artefatos de imagens e/ou discos ativos. Garanta que esses artefatos sejam removidos do repositório de imagens antes de excluir essa conta de armazenamento*.

**No Portal Clássico do Azure**:

*A conta de armazenamento com falha no envio <vm-storage-account-name> tem um contêiner com artefatos de imagens e/ou discos ativos. Garanta que esses artefatos sejam removidos do repositório de imagens antes de excluir essa conta de armazenamento. Quando você tentar excluir uma conta de armazenamento e ainda houver discos ativos associados a ela, uma mensagem informando que há discos ativos que precisam ser excluídos será exibida*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Cenário 2: Não é possível excluir um contêiner
Ao tentar excluir o contêiner de armazenamento, o seguinte erro poderá ser exibido:

*Falha ao excluir o contêiner de armazenamento <container name>. Erro: 'Atualmente, há uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação*.

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Cenário 3: Não é possível excluir um VHD
Depois de excluir uma VM e então tentar excluir os blobs para os VHDs associados, você pode receber a seguinte mensagem:

*Falha ao excluir o blob 'path/XXXXXX-XXXXXX-os-1447379084699.vhd'. Erro: 'Atualmente, há uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação*.

## <a name="solution"></a>Solução
Para resolver os problemas mais comuns, tente o seguinte método:

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Etapa 1: Excluir todos os discos de SO que estejam impedindo a exclusão da conta de armazenamento, do contêiner ou do VHD
1. Alterne para o [portal clássico do Azure](https://manage.windowsazure.com/).
2. Selecione **MÁQUINA VIRTUAL** > **DISCOS**.
   
    ![Imagem de discos em máquinas virtuais no portal clássico do Azure.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)
3. Localize os discos associados à conta de armazenamento, ao contêiner ou ao VHD que você quer excluir. Ao verificar o local do disco, você encontrará a conta de armazenamento, o contêiner ou o VHD associado.
   
    ![Imagem que mostra informações sobre o local para os discos no portal clássico do Azure](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)
4. Verifique se não há VM listada no campo **Conectado a** dos discos e então exclua os discos.
   
   > [!NOTE]
   > Se um disco estiver conectado a uma VM, você não poderá excluí-lo. Discos são desconectados de uma VM excluída assincronamente. A limpeza desse campo pode levar alguns minutos depois da exclusão de uma VM.
   > 
   > 

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Etapa 2: Excluir todas as imagens de VM que estão impedindo a exclusão da conta de armazenamento ou do contêiner
1. Alterne para o [portal clássico do Azure](https://manage.windowsazure.com/).
2. Escolha **MÁQUINA VIRTUAL** > **IMAGENS** e exclua as imagens associadas à conta de armazenamento, ao contêiner ou ao VHD.
   
    Depois disso, tente excluir a conta de armazenamento, o contêiner ou o VHD novamente.

> [!WARNING]
> Não se esqueça de fazer backup de todas as informações que você deseja salvar antes de excluir a conta. Não é possível restaurar uma conta de armazenamento excluída nem recuperar qualquer conteúdo que ela tinha antes da exclusão. Isso também vale para todos os recursos na conta: depois de excluir um VHD, um blob, uma tabela, uma fila ou um arquivo, eles são permanentemente excluídos. Assegure-se de que o recurso não esteja em uso.
> 
> 

## <a name="about-the-stopped-deallocated-status"></a>Sobre o status Parado (desalocado)
As VMs que foram criadas no modelo de implantação clássico e retidas terão o status **Parado (desalocado)** no [portal do Azure](https://portal.azure.com/) ou no [portal clássico do Azure](https://manage.windowsazure.com/).

**Portal Clássico do Azure**:

![Status Parado (Desalocado) para VMs no portal do Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

**Portal do Azure**:

![Status Parado (desalocado) para VMs no portal clássico do Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Um status "Parado (desalocado)" libera recursos do computador, como CPU, memória e rede. No entanto, os discos ainda são mantidos para que você possa recriar rapidamente a VM, se necessário. Esses discos são criados sobre os VHDs cujos backups são feitos pelo armazenamento do Azure. A conta de armazenamento tem esses VHDs e os discos têm concessões nesses VHDs.

## <a name="next-steps"></a>Próximas etapas
* [Excluir uma conta de armazenamento](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Como interromper a liberação de bloqueio do armazenamento de blobs no Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)




<!--HONumber=Nov16_HO3-->


