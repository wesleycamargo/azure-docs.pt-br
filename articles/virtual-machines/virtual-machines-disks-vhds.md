<properties
	pageTitle="Sobre discos e VHDs para Máquinas Virtuais"
	description="Conheça o básico sobre discos e VHDs para máquinas virtuais no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="kathydav"/>

# Sobre discos e VHDs para Máquinas Virtuais

Todas as máquinas virtuais no Azure são configuradas com pelo menos dois discos quando você cria a máquina virtual – um é um disco do sistema operacional e o outro é um disco local temporário, às vezes chamado de disco de recursos. O disco do sistema operacional é criado por meio de uma imagem, e o disco do sistema operacional e a imagem na verdade são VHDs (discos rígidos virtuais) armazenados em uma conta de armazenamento do Azure. Máquinas virtuais também podem ter discos de dados, e eles também são armazenados em VHDs.

>[AZURE.WARNING]Não armazene dados no disco temporário. Ele fornece armazenamento temporário para aplicativos e processos e destina-se apenas armazenar dados como arquivos de paginação ou de permuta. Para remapear este disco para uma VM do Windows, consulte [Alterar a letra da unidade de disco temporário do Windows](virtual-machines-windows-change-drive-letter.md).

## Sobre discos

Assim como qualquer outro computador, as máquinas virtuais do Azure usam os discos como locais onde armazenar um sistema operacional, aplicativos e dados. Todas as máquinas virtuais do Azure têm pelo menos dois discos - um disco do sistema operacional e um disco temporário. Elas também podem ter um ou mais discos de dados.

- **Disco do sistema operacional** - Cada máquina virtual tem um disco de sistema operacional anexado. Ele é registrado como uma unidade SATA e rotulado como a unidade C. Este disco tem uma capacidade máxima de 1023 gibibytes. Quando o Azure cria um disco do sistema operacional, três cópias do disco são criadas para maior durabilidade. Além disso, se você configurar a máquina virtual para replicação geográfica, seu VHD também será replicado em sites diferentes, distantes mais de 400 milhas.
- O **disco temporário** é criado automaticamente para você. Nas máquinas virtuais do Windows, esse disco é rotulado como unidade D. Nas máquinas virtuais Linux, o disco normalmente é /dev/sdb e é formatado e montado em /mnt/resource pelo Agente Linux do Azure.
- Um **disco de dados** é um VHD anexado a uma máquina virtual para armazenar dados de aplicativo ou outros dados que você precisa manter. Discos de dados são registrados como unidades SCSI e rotulados com a letra que você escolher. Cada disco de dados tem uma capacidade máxima de 1023 gibibytes. O tamanho da máquina virtual determina quantos discos de dados você pode anexar a ele e o tipo de armazenamento que pode usar para hospedar os discos.

	Para obter mais detalhes sobre as capacidades das VMs, consulte [Tamanhos das Máquinas Virtuais](virtual-machines-size-specs.md).

Quando você cria uma máquina virtual por meio de uma imagem, o Azure cria um disco do sistema operacional. Se você usar uma imagem que inclui discos de dados, o Azure também cria os discos de dados quando cria a máquina virtual. (Você pode usar uma imagem do Azure, de um parceiro ou uma que você mesmo fornecer.) Caso contrário, adicione discos de dados após criar a máquina virtual.

Você pode adicionar discos de dados a uma máquina virtual a qualquer momento "anexando" o disco à máquina virtual. Você pode usar um VHD que você carregou ou copiou para sua conta de armazenamento ou um que o Azure cria para você. Anexar um disco de dados associa o arquivo de VHD da sua conta de armazenamento à máquina virtual, fazendo uma "concessão" do VHD de modo que ele não possa ser excluído do armazenamento enquanto estiver anexado a uma máquina virtual.

## Sobre VHDs

Os VHDs usados no Azure são arquivos .vhd armazenados como blobs de página em uma conta de armazenamento padrão ou premium no Azure. (O armazenamento premium está disponível em determinadas regiões.) Para conhecer mais detalhes sobre os blobs, consulte [Noções Gerais sobre Blobs de Blocos e Blobs de Páginas](https://msdn.microsoft.com/library/ee691964.aspx). Para obter detalhes sobre o armazenamento premium, confira consulte [Armazenamento Premium: Armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../storage-premium-storage-preview-portal.md).

Fora do Azure, os discos rígidos virtuais podem usar um formato VHD ou VHDX. Eles também podem ser fixos, ter expansão dinâmica ou diferenciação. O Azure é compatível com discos fixos de formato VHD. O formato fixo dispõe o disco lógico linearmente dentro do arquivo, de forma que o deslocamento X do disco é armazenado no deslocamento X do blob. Um pequeno rodapé ao final do blob descreve as propriedades do VHD. Frequentemente, o formato fixo desperdiça espaço, porque muitos discos têm em si grandes intervalos não usados. No entanto, o Azure armazena arquivos .vhd em um formato livre, assim você obtém os benefícios de discos fixos e dinâmicos ao mesmo tempo. Para obter mais detalhes, consulte [Introdução ao HDInsight com Discos Rígidos Virtuais](https://technet.microsoft.com/library/dd979539.aspx).

Todos os arquivos .vhd no Azure que você deseja usar como fonte para criar discos ou imagens são somente leitura. Quando você cria um disco ou uma imagem, o Azure faz cópias dos arquivos .vhd. Essas cópias podem ser somente leitura ou de leitura e gravação, dependendo de como você usar o VHD.

 Quando você cria uma máquina virtual por meio de uma imagem, o Azure cria um disco para a máquina virtual que é uma cópia do arquivo .vhd de origem. Para proteger contra exclusão acidental, o Azure faz uma concessão de qualquer arquivo .vhd de origem que é usado para criar uma imagem, um disco de sistema operacional ou um disco de dados.

Antes de excluir um arquivo .vhd de origem, você precisará remover a concessão excluindo o disco ou a imagem. Para excluir um arquivo .vhd que está sendo usado por uma máquina virtual como disco do sistema operacional, você pode excluir a máquina virtual, o disco do sistema operacional e o arquivo .vhd de origem de uma só vez excluindo a máquina virtual e todos os discos associados. No entanto, a exclusão de um arquivo .vhd que é a origem de um disco de dados requer várias etapas em uma determinada ordem – desanexar o disco da máquina virtual, excluir o disco e depois excluir o arquivo .vhd.

>[AZURE.WARNING]Se você excluir um .vhd de origem do armazenamento ou excluir sua conta de armazenamento, a Microsoft não poderá recuperar esses dados para você.

## Próximas etapas

VMs do Linux:

-  [Anexar um disco e prepará-lo para uso](virtual-machines-linux-how-to-attach-disk.md)
-  [Capturar uma Máquina Virtual Linux](virtual-machines-linux-capture-image.md)
-  [Desanexar um disco](virtual-machines-linux-how-to-detach-disk.md)

VMs do Windows:

-  [Anexar um disco e prepará-lo para uso](storage-windows-attach-disk.md)
-  [Capturar uma Máquina Virtual do Windows](virtual-machines-capture-image-windows-server.md)
-  [Desanexar um disco](storage-windows-detach-disk.md)

<!---HONumber=August15_HO6-->