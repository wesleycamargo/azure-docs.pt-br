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
ms.date: 11/21/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 822bace005a6244a47c9484487dab85b1aec9d9a
ms.openlocfilehash: e20b1ca582c56da7b4fb1e2df3be90bd1c29a8b6


---
# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Solucionar problemas ao excluir contas de armazenamento, contêineres ou VHDs do Azure em uma implantação clássica
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Você pode receber erros ao tentar excluir a conta de armazenamento, o contêiner ou o VHD do Azure no [portal do Azure](https://portal.azure.com/) ou no [portal clássico do Azure](https://manage.windowsazure.com/). Os problemas podem ser causados pelas seguintes circunstâncias:

* Ao excluir uma VM, o disco e o VHD não são automaticamente excluídos. Esse pode ser o motivo da falha na exclusão da conta de armazenamento. Não excluímos o disco para que você possa usá-lo para montar outra VM.
* Ainda há uma concessão em um disco ou o blob que está associado ao disco.
* Ainda há uma imagem de VM que está usando uma conta de armazenamento, contêiner ou blob.

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

ou o

*Os seguintes discos da máquina virtual usam blobs nesse contêiner, portanto o contêiner não pode ser excluído: VirtualMachineDiskName1, VirtualMachineDiskName2, ...*

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Cenário 3: Não é possível excluir um VHD
Depois de excluir uma VM e então tentar excluir os blobs para os VHDs associados, você pode receber a seguinte mensagem:

*Falha ao excluir o blob 'path/XXXXXX-XXXXXX-os-1447379084699.vhd'. Erro: 'Atualmente, há uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação*.

ou o

*O blob 'BlobName.vhd' está em uso como o disco de máquina virtual ‘VirtualMachineDiskName’, portanto não é possível excluir o blob.*

## <a name="solution"></a>Solução
Para resolver os problemas mais comuns, tente o seguinte método:

### <a name="step-1-delete-any-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Etapa 1: Excluir os discos que estão impedindo a exclusão da conta de armazenamento, do contêiner ou do VHD
1. Alterne para o [portal clássico do Azure](https://manage.windowsazure.com/).
2. Selecione **MÁQUINA VIRTUAL** > **DISCOS**.

    ![Imagem de discos em máquinas virtuais no portal clássico do Azure.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)
3. Localize os discos associados à conta de armazenamento, ao contêiner ou ao VHD que você quer excluir. Ao verificar o local do disco, você encontrará a conta de armazenamento, o contêiner ou o VHD associado.

    ![Imagem que mostra informações sobre o local para os discos no portal clássico do Azure](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)
4. Exclua os discos usando um dos seguintes métodos:

  - Se não houver nenhuma VM listada no campo **Conectado a** do disco, você poderá excluir os discos diretamente.

  - Se o disco for um disco de dados, siga estas etapas:

    1. Verifique o nome da VM à qual o disco está anexado.
    2. Acesse **Máquinas Virtuais** > **Instâncias** e localize a VM.
    3. Verifique se o disco não está sendo usado ativamente.
    4. Selecione **Desanexar Disco** na parte inferior do portal para desanexar o disco.
    5. Acesse **Máquinas Virtuais** > **Discos** e aguarde o campo **Conectado a** ficar em branco. Isso indica que o disco foi desanexado com êxito da VM.
    6. Selecione **Excluir** na parte inferior de **Máquinas Virtuais** > **Discos** para excluir o disco.

  - Se o disco for um disco de sistema operacional (o campo **Contém sistema operacional** terá um valor como Windows) e estiver anexado a uma VM, siga estas etapas para excluir a VM. O disco de sistema operacional não pode ser desanexado, portanto é preciso excluir a VM para liberar a concessão.

    1. Verifique o nome da máquina virtual à qual o disco de dados está anexado.  
    2. Acesse **Máquinas Virtuais** > **Instâncias** e selecione a VM à qual o disco está anexado.
    3. Certifique-se de que nada esteja usando ativamente a máquina virtual, e que você não precise mais da máquina virtual.
    4. Selecione a VM à qual o disco está anexado e selecione **Excluir** > **Excluir os discos conectados**.
    5. Acesse **Máquinas Virtuais** > **Discos** e aguarde o disco desaparecer.  Pode levar alguns minutos para que isso ocorra e talvez seja necessário atualizar a página.
    6. Se o disco não desaparecer, aguarde o campo **Conectado a** ficar em branco. Isso indica que o disco foi totalmente desanexado da VM.  Em seguida, selecione o disco e selecione **Excluir** na parte inferior da página para excluir o disco.


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



<!--HONumber=Nov16_HO4-->


