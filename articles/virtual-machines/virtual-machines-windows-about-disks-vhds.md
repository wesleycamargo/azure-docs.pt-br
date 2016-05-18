<properties
	pageTitle="Sobre discos e VHDs para VMs do Windows | Microsoft Azure"
	description="Conheça o básico sobre discos e VHDs para máquinas virtuais do Windows no Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="cynthn"/>

# Sobre discos e VHDs para máquinas virtuais do Azure

Assim como qualquer outro computador, as máquinas virtuais do Azure usam os discos como locais onde armazenar um sistema operacional, aplicativos e dados. Todas as máquinas virtuais do Azure têm pelo menos dois discos - um disco do sistema operacional Windows (no caso de uma VM do Windows) e um disco temporário. O disco do sistema operacional é criado por meio de uma imagem, e o disco do sistema operacional e a imagem na verdade são VHDs (discos rígidos virtuais) armazenados em uma conta de armazenamento do Azure. Máquinas virtuais também podem ter um ou mais discos de dados que também são armazenados em VHDs. Este artigo também está disponível para [máquinas virtuais Linux](virtual-machines-linux-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## Próximas etapas
-  [Capture uma máquina virtual do Windows](virtual-machines-windows-capture-image.md) para que você possa escalar horizontalmente sua implantação de VM.
-  [Carregue uma imagem de VM do Windows no Azure](virtual-machines-windows-upload-image.md) que será usada durante a criação de uma nova VM.
-  [Altere a letra da unidade do disco temporário do Windows](virtual-machines-windows-classic-change-drive-letter.md) para que seu aplicativo possa usar a unidade D: para dados.

<!---HONumber=AcomDC_0413_2016-->