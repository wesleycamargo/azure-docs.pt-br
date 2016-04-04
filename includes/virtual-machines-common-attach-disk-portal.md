

Este artigo mostra como anexar discos novos e existentes em uma máquina virtual por meio do portal do Azure. Antes de fazer isso, revise estas dicas:

- O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md).
- Para usar o Armazenamento Premium, você precisará de uma máquina virtual da série DS ou GS. Você pode usar discos de contas de armazenamento Premium e Standard com essas máquinas virtuais. O armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../storage/storage-premium-storage-preview-portal.md).
- Discos anexados às máquinas virtuais são, na verdade, os arquivos. vhd em uma conta de armazenamento do Azure. Para obter detalhes, veja [Sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md).
- Para um novo disco, você não precisa criá-lo primeiro porque o Azure cria quando você anexa o mesmo.
- Para um disco existente, o arquivo. vhd deve estar disponível em uma conta de armazenamento do Azure. Você pode usar um .vhd que já existe, se ele não estiver anexado a outra máquina virtual, ou carregar seu próprio arquivo .vhd na conta de armazenamento.

## Localizar a máquina virtual

1. Entre no Portal do Azure.

2. No menu Hub, clique em **Máquinas Virtuais**.

3.	Selecione a máquina virtual na lista.

4. À direita, em **Essentials**, clique em **Todas as configurações**, e, em seguida, clique em **Discos**.

	![Abrir configurações de disco](./media/virtual-machines-common-attach-disk-portal/find-disk-settings.png)

Continue seguindo as instruções para anexar um novo disco ou um disco existente.

## Opção 1: Anexar um novo disco

1.	Na folha **Discos**, clique em **Anexar novo**.

2.	Examine as configurações padrão, atualize conforme necessário e clique em **OK**.

 	![Analisar configurações de disco](./media/virtual-machines-common-attach-disk-portal/attach-new.png)

3.	Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **Discos de Dados**.

## Opção 2: Anexar um disco existente

1.	Na folha **Discos**, clique em **Anexar existente**.

2.	Em **Anexar disco existente**, clique em **Arquivo VHD**.

	![Anexar disco existente](./media/virtual-machines-common-attach-disk-portal/attach-existing.png)

3.	Em **Contas de armazenamento**, selecione a conta e o contêiner que mantém o arquivo .vhd.

	![Localização do VHD](./media/virtual-machines-common-attach-disk-portal/find-storage-container.png)

4.	Selecionar o arquivo .vhd

5.	Em **Anexar disco existente**, o arquivo que você selecionou é listado em **Arquivo VHD**. Clique em **OK**.

6.	Depois que o Azure anexa o disco à máquina virtual, ele é listado nas configurações de disco da máquina virtual em **Discos de Dados**.

<!---HONumber=AcomDC_0323_2016-->