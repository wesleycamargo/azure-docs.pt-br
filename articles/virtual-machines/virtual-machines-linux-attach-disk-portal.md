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
ms.date: 07/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 743f6abb871bdc4f65f302f0c1efdc4603c52626


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Como anexar um disco de dados a uma VM Linux no Portal do Azure
Este artigo mostra como anexar discos novos e existentes a uma máquina virtual Linux por meio do portal do Azure. Você também pode [anexar um disco de dados a uma VM do Windows no Portal do Azure](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Antes de fazer isso, revise estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para usar o Armazenamento Premium, você precisará de uma máquina virtual da série DS ou GS. Você pode usar discos de contas de armazenamento Premium e Standard com essas máquinas virtuais. O armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../storage/storage-premium-storage.md).
* Discos anexados às máquinas virtuais são, na verdade, os arquivos. vhd em uma conta de armazenamento do Azure. Para obter detalhes, confira [Sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para um novo disco, você não precisa criá-lo primeiro porque o Azure cria quando você anexa o mesmo.
* Para um disco existente, o arquivo. vhd deve estar disponível em uma conta de armazenamento do Azure. Você pode usar um .vhd que já existe, se ele não estiver anexado a outra máquina virtual, ou carregar seu próprio arquivo .vhd na conta de armazenamento.

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Próximas etapas
Depois que o disco for adicionado, você precisará prepará-lo para uso. Veja no sistema operacional da máquina virtual: "Como inicializar um novo disco de dados no Linux" neste [artigo](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux).



<!--HONumber=Nov16_HO3-->


