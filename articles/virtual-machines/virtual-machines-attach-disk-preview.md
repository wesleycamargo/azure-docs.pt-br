<properties
	pageTitle="Como anexar um disco de dados no portal de visualização do Azure"
	description="Como anexar discos de dados novos e existentes no portal de visualização do Azure | Microsoft Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2015"
	ms.author="cynthn"/>

# Como anexar um disco de dados no portal de visualização do Azure

Este artigo mostra como anexar discos novos e existentes em uma máquina virtual por meio do portal de visualização do Azure. Antes de fazer isso, revise estas dicas:

- O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-size-specs.md).
- Para usar o Armazenamento Premium, você precisará de uma máquina virtual da série DS ou GS. Você pode usar discos de contas de armazenamento Premium e Standard com essas máquinas virtuais. O armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../storage/storage-premium-storage-preview-portal.md).
- Discos anexados às máquinas virtuais são, na verdade, os arquivos. vhd em uma conta de armazenamento do Azure. Para obter detalhes, consulte [Sobre discos e VHDs para Máquinas Virtuais](virtual-machines-disks-vhds.md).
- Para um novo disco, você não precisa criá-lo primeiro porque o Azure cria quando você anexa o mesmo.
- Para um disco existente, o arquivo. vhd deve estar disponível em uma conta de armazenamento do Azure. Você pode usar um que já existe, se ele não estiver anexado a outra máquina virtual ou carregar seu próprio arquivo .vhd na conta de armazenamento.

## Localizar a máquina virtual

1. Entre no [Portal de visualização](https://portal.azure.com).

2. No menu Hub, clique em **Procurar**.

3. Na folha de pesquisa, role a tela para baixo e clique em **Máquinas Virtuais**.

	![Procurar máquinas virtuais](./media/virtual-machines-attach-disk-preview/search-blade-preview-portal.png)

4.	Selecione a máquina virtual na lista.

5. À direita, em **Essentials**, clique em **Todas as configurações**, e, em seguida, clique em **Discos**.

	![Abrir configurações de disco](./media/virtual-machines-attach-disk-preview/find-disk-settings.png)

Continue seguindo as instruções para anexar um novo disco ou um disco existente.

## Opção 1: Anexar um novo disco

1.	Na folha **discos**, clique em **Anexar novo**.

2.	Examine as configurações padrões, atualize conforme necessário e, em seguida, clique em **OK**.

 	![Analisar configurações de disco](./media/virtual-machines-attach-disk-preview/attach-new.png)

3.	Depois que o Azure cria o disco e anexa-o à máquina virtual, o novo disco é listado em configurações de disco da máquina virtual em **Discos de dados**.

## Opção 2: Anexar um disco existente

1.	Na folha **Discos**, clique em **Anexar existente**.

2.	Em **Anexar disco existente**, clique em **Arquivo VHD**.

	![Anexar disco existente](./media/virtual-machines-attach-disk-preview/attach-existing.png)

3.	Em **Contas de armazenamento**, selecione a conta e o contêiner que mantém o arquivo .vhd.

	![Localização do VHD](./media/virtual-machines-attach-disk-preview/find-storage-container.png)

4.	Selecionar o arquivo .vhd

5.	Em **Anexar disco existente**, o arquivo que você selecionou é listado em **Arquivo VHD**. Clique em **OK**.

6.	Depois que o Azure anexa o disco à máquina virtual, o disco é listado em configurações de disco da máquina virtual em **Discos de dados**.

## Próximas etapas

Depois que o disco é adicionado, é necessário prepará-lo para uso em um sistema operacional da máquina virtual:

- Para o Linux, consulte "Como: inicializar um novo disco de dados no Linux" neste [artigo](virtual-machines-linux-how-to-attach-disk.md).
- Para o Windows, consulte "Como: Inicializar um novo disco de dados no Windows Server" neste [artigo](storage-windows-attach-disk.md).

## Recursos adicionais

[Sobre as contas de armazenamento do Azure]

<!--Link references-->

[Sobre as contas de armazenamento do Azure]: ../storage-whatis-account/

<!---HONumber=Sept15_HO3-->