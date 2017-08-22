---
title: Anexar um disco de dados a uma VM do Linux | Microsoft Docs
description: "Como anexar um disco de dados novo ou existente a uma VM do Linux no portal do Azure usando o modelo de implantação do Gerenciador de Recursos."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: fb3b4ea612777d0ba801342dacf4b2ece46afed6
ms.contentlocale: pt-br
ms.lasthandoff: 08/11/2017

---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Como anexar um disco de dados a uma VM Linux no Portal do Azure
Este artigo mostra como anexar discos novos e existentes a uma máquina virtual Linux por meio do portal do Azure. Você também pode [anexar um disco de dados a uma VM do Windows no Portal do Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Você pode optar por usar o Azure Managed Disks ou discos não gerenciado. O Managed Disks são tratados pela plataforma do Azure e não exigem nenhuma preparação ou local para armazenamento. Discos não gerenciados exigem uma conta de armazenamento e têm algumas [cotas e limites que se aplicam](../../azure-subscription-service-limits.md#storage-limits). Para saber mais sobre Azure Managed Disks, veja [Visão geral dos Azure Managed Disks](../../storage/storage-managed-disks-overview.md).

Antes de anexar discos à sua VM, veja estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para usar o Armazenamento Premium, você precisa de uma máquina virtual da série DS ou GS. Você pode usar discos Premium e Standard com essas máquinas virtuais. O armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Discos anexados às máquinas virtuais são, na verdade, os arquivos. vhd armazenados no Azure. Para obter detalhes, confira [Sobre discos e VHDs para máquinas virtuais](../../storage/storage-about-disks-and-vhds-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Máquinas Virtuais**.
3. Selecione a máquina virtual na lista.
4. Na folha Máquinas Virtuais, em **Essentials**, clique em **Discos**.
   
    ![Abrir configurações de disco](./media/attach-disk-portal/find-disk-settings.png)

Continue seguindo as instruções para anexar um [disco gerenciado](#use-azure-managed-disks) ou um [disco não gerenciado](#use-unmanaged-disks).

## <a name="use-azure-managed-disks"></a>Usar o Azure Managed Disks

### <a name="attach-a-new-disk"></a>Anexar um novo disco

1. Na folha **Discos**, clique em **+ Adicionar disco de dados**.
2. Clique no menu suspenso para **Nome** e selecione **Criar disco**:

    ![Criar disco gerenciado do Azure](./media/attach-disk-portal/create-new-md.png)

3. Insira um nome para o disco gerenciado. Examine as configurações padrão, atualize conforme necessário e clique em **Criar**.
   
   ![Analisar configurações de disco](./media/attach-disk-portal/create-new-md-settings.png)

4. Clique em **Salvar** para criar o disco gerenciado e atualizar a configuração da VM:

   ![Salvar novo Azure Managed Disk](./media/attach-disk-portal/confirm-create-new-md.png)

5. Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **Discos de Dados**. Como os discos gerenciados são um recurso de nível superior, o disco aparece na raiz do grupo de recursos:

   ![Azure Managed Disk no grupo de recursos](./media/attach-disk-portal/view-md-resource-group.png)

### <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. Na folha **Discos**, clique em **+ Adicionar disco de dados**.
2. Clique no menu suspenso para **Nome** para exibir uma lista de discos gerenciados existentes acessíveis à sua assinatura do Azure. Selecione o disco gerenciado a ser anexado:

   ![Anexar um Azure Managed Disk existente](./media/attach-disk-portal/select-existing-md.png)

3. Clique em **Salvar** para anexar o disco gerenciado existente e atualizar a configuração da VM:
   
   ![Salvar atualizações do Azure Managed Disk](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Depois que o Azure anexa o disco à máquina virtual, ele é listado nas configurações de disco da máquina virtual em **Discos de Dados**.

## <a name="use-unmanaged-disks"></a>Usar discos não gerenciados

### <a name="attach-a-new-disk"></a>Anexar um novo disco

1. Na folha **Discos**, clique em **+ Adicionar disco de dados**.
2. Examine as configurações padrão, atualize conforme necessário e clique em **OK**.
   
   ![Analisar configurações de disco](./media/attach-disk-portal/attach-new.png)
3. Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **Discos de Dados**.

### <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. Na folha **Discos**, clique em **+ Adicionar disco de dados**.
2. Em **Anexar disco existente**, clique em **Arquivo VHD**.
   
   ![Anexar disco existente](./media/attach-disk-portal/attach-existing.png)
3. Em **Contas de armazenamento**, selecione a conta e o contêiner que mantém o arquivo .vhd.
   
   ![Localização do VHD](./media/attach-disk-portal/find-storage-container.png)
4. Selecionar o arquivo .vhd
5. Em **Anexar disco existente**, o arquivo que você selecionou é listado em **Arquivo VHD**. Clique em **OK**.
6. Depois que o Azure anexa o disco à máquina virtual, ele é listado nas configurações de disco da máquina virtual em **Discos de Dados**.


## <a name="next-steps"></a>Próximas etapas
Depois que o disco for adicionado, você precisará prepará-lo para uso. Para obter mais informações, veja [Como: inicializar um novo disco de dados no Linux](add-disk.md).

