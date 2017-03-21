---
title: Sobre discos e VHDs para VMs com Linux do Microsoft Azure | Microsoft Docs
description: "Conheça o básico sobre discos e VHDs para máquinas virtuais Linux no Azure."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 7be8dd52-98f7-4187-9b78-55197915bc9b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: e34a2bfbf5f1ae544a729c994d91c485d48bb440
ms.openlocfilehash: 7cd157cd1c5730443a710ac46d923319789aec61
ms.lasthandoff: 03/01/2017


---
# <a name="about-disks-and-vhds-for-azure-linux-vms"></a>Sobre discos e VHDs para VMs com Linux do Azure
Assim como qualquer outro computador, as máquinas virtuais do Azure usam os discos como locais onde armazenar um sistema operacional, aplicativos e dados. Todas as máquinas virtuais do Azure têm pelo menos dois discos - um disco do sistema operacional Linux e um disco temporário. O disco do sistema operacional é criado por meio de uma imagem, e o disco do sistema operacional e a imagem na verdade são VHDs (discos rígidos virtuais) armazenados em uma conta de armazenamento do Azure. Máquinas virtuais também podem ter um ou mais discos de dados que também são armazenados em VHDs. 

Neste artigo, vamos falar sobre os diferentes usos dos discos e discutir os diferentes tipos de discos que você pode criar e usar. Este artigo também está disponível para [máquinas virtuais do Windows](storage-about-disks-and-vhds-windows.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Discos usados por VMs

Vamos dar uma olhada em como os discos são usados pelas máquinas virtuais.

## <a name="operating-system-disk"></a>Disco do sistema operacional
Cada máquina virtual tem um disco de sistema operacional anexado. Ele é registrado como uma unidade SATA e rotulado como /dev/sda por padrão. Este disco tem uma capacidade máxima de 1023 GB (Gigabytes). 

## <a name="temporary-disk"></a>Disco temporário
Cada VM contém um disco temporário. O disco temporário fornece armazenamento de curto prazo para aplicativos e processos e destina-se apenas a armazenar dados como arquivos de paginação ou de permuta. Os dados no disco temporário podem ser perdidos durante um [evento de manutenção](../virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-planned-vs-unplanned-maintenance) ou durante a [nova implantação de uma VM](../virtual-machines/virtual-machines-linux-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Durante a reinicialização padrão da VM, os dados na unidade temporária devem persistir.

Em máquinas virtuais Linux, normalmente, o disco é **/dev/sdb** e é formatado e montado em **/mnt** pelo Agente Linux do Azure. O tamanho do disco temporário varia com base no tamanho da máquina virtual. Para saber mais, confira [Tamanhos de máquinas virtuais do Linux](../virtual-machines/virtual-machines-linux-sizes.md).

Para obter mais informações sobre como o Azure usa o disco temporário, consulte [Noções básicas sobre a unidade temporária nas Máquinas Virtuais do Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco de dados
Um disco de dados é um VHD anexado a uma máquina virtual para armazenar dados de aplicativo ou outros dados que você precisa manter. Discos de dados são registrados como unidades SCSI e rotulados com a letra que você escolher. Cada disco de dados tem uma capacidade máxima de 1023 GB. O tamanho da máquina virtual determina quantos discos de dados você pode anexar a ele e o tipo de armazenamento que pode usar para hospedar os discos.

> [!NOTE]
> Para obter mais detalhes sobre as capacidades de máquinas virtuais, consulte [Tamanhos das máquinas virtuais do Linux](../virtual-machines/virtual-machines-linux-sizes.md).
> 

Quando você cria uma máquina virtual por meio de uma imagem, o Azure cria um disco do sistema operacional. Se você usar uma imagem que inclui discos de dados, o Azure também cria os discos de dados quando cria a máquina virtual. Caso contrário, adicione discos de dados após criar a máquina virtual.

Você pode adicionar discos de dados a uma máquina virtual a qualquer momento **anexando** o disco à máquina virtual. Você pode usar um VHD que você carregou ou copiou para sua conta de armazenamento ou um que o Azure cria para você. Anexar um disco de dados associa o arquivo de VHD com a VM, fazendo uma "concessão" do VHD de modo que ele não possa ser excluído do armazenamento enquanto estiver anexado.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Solucionar problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Próximas etapas
* [Anexar um disco](../virtual-machines/virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para adicionar mais armazenamento à sua VM.
* [Configure o RAID de software](../virtual-machines/virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para redundância.
* [Capturar uma máquina virtual do Linux](../virtual-machines/virtual-machines-linux-classic-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para implantar rapidamente VMs adicionais.


