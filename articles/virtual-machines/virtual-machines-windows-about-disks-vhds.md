---
title: Sobre discos e VHDs para VMs do Windows | Microsoft Docs
description: Conheça o básico sobre discos e VHDs para máquinas virtuais do Windows no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>Sobre discos e VHDs para máquinas virtuais do Azure
Assim como qualquer outro computador, as máquinas virtuais do Azure usam os discos como locais onde armazenar um sistema operacional, aplicativos e dados. Todas as máquinas virtuais do Azure têm pelo menos dois discos – um disco do sistema operacional Windows e um disco temporário. O disco do sistema operacional é criado por meio de uma imagem, e o disco do sistema operacional e a imagem são VHDs (discos rígidos virtuais) armazenados em uma conta de armazenamento do Azure. Máquinas virtuais também podem ter um ou mais discos de dados que também são armazenados em VHDs. Este artigo também está disponível para [máquinas virtuais Linux](virtual-machines-linux-about-disks-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="operating-system-disk"></a>Disco do sistema operacional
Cada máquina virtual tem um disco de sistema operacional anexado. Ele é registrado como uma unidade SATA e rotulado como a unidade C: por padrão. Este disco tem uma capacidade máxima de 1023 GB (Gigabytes). 

## <a name="temporary-disk"></a>Disco temporário
O disco temporário é criado automaticamente para você. O disco temporário é rotulado como a unidade D: por padrão e é usado para armazenar o arquivo pagefile.sys. 

O tamanho do disco temporário varia com base no tamanho da máquina virtual. Para obter mais informações, consulte [Tamanhos de máquinas virtuais do Windows](virtual-machines-windows-sizes.md).

> [!WARNING]
> Não armazene dados no disco temporário. Ele fornece armazenamento temporário para aplicativos e processos e destina-se apenas armazenar dados como arquivos de paginação ou de permuta. Para remapear este disco para uma letra de unidade diferente, consulte [Alterar a letra da unidade de disco temporário do Windows](virtual-machines-windows-classic-change-drive-letter.md).
> 
> 

Para obter mais informações sobre como o Azure usa o disco temporário, consulte [Noções básicas sobre a unidade temporária nas Máquinas Virtuais do Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco de dados
Um disco de dados é um VHD anexado a uma máquina virtual para armazenar dados de aplicativo ou outros dados que você precisa manter. Discos de dados são registrados como unidades SCSI e rotulados com a letra que você escolher.  Cada disco de dados tem uma capacidade máxima de 1023 GB. O tamanho da máquina virtual determina quantos discos de dados você pode anexar a ele e o tipo de armazenamento que pode usar para hospedar os discos.

> [!NOTE]
> Para obter mais informações sobre as capacidades de máquinas virtuais, veja [Tamanhos das máquinas virtuais do Windows](virtual-machines-windows-sizes.md).
> 
> 

Quando você cria uma máquina virtual por meio de uma imagem, o Azure cria um disco do sistema operacional. Se você usar uma imagem que inclui discos de dados, o Azure também cria os discos de dados quando cria a máquina virtual. Caso contrário, adicione discos de dados após criar a máquina virtual.

Você pode adicionar discos de dados a uma máquina virtual a qualquer momento **anexando** o disco à máquina virtual. Você pode usar um VHD que você carregou ou copiou para sua conta de armazenamento ou um que o Azure cria para você. Anexar um disco de dados associa o arquivo de VHD com a VM, fazendo uma "concessão" do VHD de modo que ele não possa ser excluído do armazenamento enquanto estiver anexado.

## <a name="about-vhds"></a>Sobre VHDs
Os VHDs usados no Azure são arquivos .vhd armazenados como blobs de página em uma conta de armazenamento padrão ou premium no Azure. Para obter mais informações sobre blobs, veja [Noções gerais sobre blobs de blocos e blobs de páginas](https://msdn.microsoft.com/library/ee691964.aspx). Para saber mais sobre o armazenamento premium, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure](../storage/storage-premium-storage.md).

O Azure dá suporte aos discos fixos no formato VHD. O formato fixo dispõe o disco lógico linearmente dentro do arquivo, de forma que o deslocamento X do disco é armazenado no deslocamento X do blob. Um pequeno rodapé ao final do blob descreve as propriedades do VHD. Frequentemente, o formato fixo desperdiça espaço, porque muitos discos têm em si grandes intervalos não usados. No entanto, o Azure armazena arquivos .vhd em um formato livre, assim você obtém os benefícios de discos fixos e dinâmicos ao mesmo tempo. Para saber mais, confira [Introdução aos discos rígidos virtuais](https://technet.microsoft.com/library/dd979539.aspx).

Todos os arquivos .vhd no Azure que você deseja usar como fonte para criar discos ou imagens são somente leitura. Quando você cria um disco ou uma imagem, o Azure faz cópias dos arquivos .vhd. Essas cópias podem ser somente leitura ou de leitura e gravação, dependendo de como você usar o VHD.

Quando você cria uma máquina virtual por meio de uma imagem, o Azure cria um disco para a máquina virtual que é uma cópia do arquivo .vhd de origem. Para proteger contra exclusão acidental, o Azure faz uma concessão de qualquer arquivo .vhd de origem que é usado para criar uma imagem, um disco de sistema operacional ou um disco de dados.

Antes de excluir um arquivo .vhd de origem, você precisará remover a concessão excluindo o disco ou a imagem. Você pode excluir a máquina virtual, o disco do sistema operacional e o arquivo .vhd de origem de uma só vez excluindo a máquina virtual e todos os discos associados. No entanto, a exclusão de um arquivo .vhd que é uma origem de um disco de dados requer várias etapas em uma ordem definida. Primeiro, desanexar o disco da máquina virtual; em seguida, excluir o disco e, por fim, excluir o arquivo .vhd.

> [!WARNING]
> Se você excluir um arquivo .vhd de origem do armazenamento ou excluir sua conta de armazenamento, a Microsoft não poderá recuperar esses dados para você.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Anexar um disco](virtual-machines-windows-attach-disk-portal.md) para adicionar mais armazenamento à sua VM.
* [Carregue uma imagem de VM do Windows no Azure](virtual-machines-windows-upload-image.md) para utilização durante a criação de uma nova VM.
* [Altere a letra da unidade do disco temporário do Windows](virtual-machines-windows-classic-change-drive-letter.md) para que seu aplicativo possa usar a unidade D: para dados.

<!--HONumber=Oct16_HO2-->


