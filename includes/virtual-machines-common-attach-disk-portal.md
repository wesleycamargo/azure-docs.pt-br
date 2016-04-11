


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

<!---HONumber=AcomDC_0330_2016-->