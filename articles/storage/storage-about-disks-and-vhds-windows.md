---
title: Sobre discos e VHDs para VMs do Windows do Microsoft Azure | Microsoft Docs
description: "Conheça o básico sobre discos e VHDs para máquinas virtuais do Windows no Azure."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 0142c64d-5e8c-4d62-aa6f-06d6261f485a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 794f87c3d4b8378d7aeca63791a8fbfd03e44ceb
ms.openlocfilehash: c226d43e8cc24af7c86ae13b3752bfd6fb53b1c8


---
# <a name="about-disks-and-vhds-for-azure-windows-vms"></a>Sobre discos e VHDs para VMs do Windows do Azure
Assim como qualquer outro computador, as máquinas virtuais do Azure usam os discos como locais onde armazenar um sistema operacional, aplicativos e dados. Todas as máquinas virtuais do Azure têm pelo menos dois discos – um disco do sistema operacional Windows e um disco temporário. O disco do sistema operacional é criado por meio de uma imagem, e o disco do sistema operacional e a imagem são VHDs (discos rígidos virtuais) armazenados em uma conta de armazenamento do Azure. Máquinas virtuais também podem ter um ou mais discos de dados que também são armazenados em VHDs. 

Neste artigo, vamos falar sobre os diferentes usos dos discos e discutir os diferentes tipos de discos que você pode criar e usar. Este artigo também está disponível para [máquinas virtuais Linux](storage-about-disks-and-vhds-linux.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Discos usados por VMs

Vamos dar uma olhada em como os discos são usados pelas VMs.

### <a name="operating-system-disk"></a>Disco do sistema operacional
Cada máquina virtual tem um disco de sistema operacional anexado. Ele é registrado como uma unidade SATA e rotulado como a unidade C: por padrão. Este disco tem uma capacidade máxima de 1023 GB (Gigabytes). 

### <a name="temporary-disk"></a>Disco temporário
O disco temporário é criado automaticamente para você. O disco temporário é rotulado como a unidade D: por padrão e é usado para armazenar o arquivo pagefile.sys. 

O tamanho do disco temporário varia com base no tamanho da máquina virtual. Para obter mais informações, consulte [Tamanhos de máquinas virtuais do Windows](../virtual-machines/virtual-machines-windows-sizes.md).

> [!WARNING]
> Não armazene dados no disco temporário. Ele fornece armazenamento temporário para aplicativos e processos e destina-se apenas armazenar dados como arquivos de paginação ou de permuta. Para remapear este disco para uma letra de unidade diferente, consulte [Alterar a letra da unidade de disco temporário do Windows](../virtual-machines/virtual-machines-windows-classic-change-drive-letter.md).
> 

Para obter mais informações sobre como o Azure usa o disco temporário, consulte [Noções básicas sobre a unidade temporária nas Máquinas Virtuais do Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

### <a name="data-disk"></a>Disco de dados
Um disco de dados é um VHD anexado a uma máquina virtual para armazenar dados de aplicativo ou outros dados que você precisa manter. Discos de dados são registrados como unidades SCSI e rotulados com a letra que você escolher. Cada disco de dados tem uma capacidade máxima de 1023 GB. O tamanho da máquina virtual determina quantos discos de dados você pode anexar a ele e o tipo de armazenamento que pode usar para hospedar os discos.

> [!NOTE]
> Para obter mais informações sobre as capacidades de máquinas virtuais, veja [Tamanhos das máquinas virtuais do Windows](../virtual-machines/virtual-machines-windows-sizes.md).
> 

Quando você cria uma máquina virtual por meio de uma imagem, o Azure cria um disco do sistema operacional. Se você usar uma imagem que inclui discos de dados, o Azure também cria os discos de dados quando cria a máquina virtual. Caso contrário, adicione discos de dados após criar a máquina virtual.

Você pode adicionar discos de dados a uma máquina virtual a qualquer momento **anexando** o disco à máquina virtual. Você pode usar um VHD que você carregou ou copiou para sua conta de armazenamento ou um que o Azure cria para você. Anexar um disco de dados associa o arquivo de VHD com a VM, fazendo uma "concessão" do VHD de modo que ele não possa ser excluído do armazenamento enquanto estiver anexado.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Uma última recomendação: use o TRIM com discos padrão não gerenciados 

Se você usar discos padrão não gerenciados (HDD), será necessário habilitar o TRIM. O TRIM descarta os blocos não usados do disco para que você seja cobrado apenas pelo armazenamento que está efetivamente sendo usado. Isso poderá representar uma economia dos custos se você criar arquivos grandes e, em seguida, excluí-los. 

Você pode executar esse comando para verificar a configuração de TRIM. Abra um prompt de comando na sua VM do Windows e digite:

```
fsutil behavior query DisableDeleteNotify
```

Se o comando retornar 0, o TRIM estará habilitado corretamente. Se ele retornar 1, execute o seguinte comando para habilitar o TRIM:

```
fsutil behavior set DisableDeleteNotify 0
```

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Próximas etapas
* [Anexar um disco](../virtual-machines/virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para adicionar mais armazenamento à sua VM.
* [Carregue uma imagem de VM do Windows no Azure](../virtual-machines/virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para utilização durante a criação de uma nova VM.
* [Altere a letra da unidade do disco temporário do Windows](../virtual-machines/virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) para que seu aplicativo possa usar a unidade D: para dados.




<!--HONumber=Feb17_HO2-->


