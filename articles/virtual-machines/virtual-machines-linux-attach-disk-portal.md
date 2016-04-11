<properties
	pageTitle="Anexar um disco de dados a uma VM do Linux | Microsoft Azure"
	description="Como anexar um disco de dados novo ou existente a uma VM do Linux no portal do Azure usando o modelo de implantação do Gerenciador de Recursos."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="cynthn"/>

# Como anexar um disco de dados a uma VM Linux no Portal do Azure

Este artigo mostra como anexar discos novos e existentes a uma máquina virtual Linux por meio do portal do Azure. Você também pode [anexar um disco de dados a uma VM do Windows no Portal do Azure](virtual-machines-windows-attach-disk-portal.md). Antes de fazer isso, revise estas dicas:

- O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md).
- Para usar o Armazenamento Premium, você precisará de uma máquina virtual da série DS ou GS. Você pode usar discos de contas de armazenamento Premium e Standard com essas máquinas virtuais. O armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../storage/storage-premium-storage-preview-portal.md).
- Discos anexados às máquinas virtuais são, na verdade, os arquivos. vhd em uma conta de armazenamento do Azure. Para obter detalhes, veja [Sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md).
- Para um novo disco, você não precisa criá-lo primeiro porque o Azure cria quando você anexa o mesmo.
- Para um disco existente, o arquivo. vhd deve estar disponível em uma conta de armazenamento do Azure. Você pode usar um .vhd que já existe, se ele não estiver anexado a outra máquina virtual, ou carregar seu próprio arquivo .vhd na conta de armazenamento.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## Próximas etapas

Depois que o disco for adicionado, você precisará prepará-lo para uso. Veja no sistema operacional da máquina virtual: “Como inicializar um novo disco de dados no Linux” neste [artigo](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux).

<!---HONumber=AcomDC_0330_2016-->