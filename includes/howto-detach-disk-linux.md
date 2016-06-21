Quando não precisar mais de um disco de dados conectado a uma VM (máquina virtual), você poderá desanexá-lo facilmente. Essa ação remove o disco da VM, mas não o remove do armazenamento. Se desejar usar os dados existentes no disco novamente, você poderá reanexá-lo à mesma VM ou anexá-lo a outra.

> [AZURE.NOTE] Uma VM no Azure usa diferentes tipos de discos, um disco de sistema operacional, um disco temporário local e discos de dados opcionais. Para obter detalhes, consulte [Sobre discos e VHDs para Máquinas Virtuais](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md). Não é possível desanexar um disco do sistema operacional, a menos que você também exclua a VM.

## Localize o disco

Antes de poder desanexar um disco de uma VM, você precisa descobrir o número LUN, que é um identificador para o disco a ser desanexado. Para fazer isso, siga estas etapas:

1. 	Abra a CLI do Azure e [conecte-se à sua assinatura do Azure](../articles/xplat-cli-connect.md). Verifique se você está no modo de Gerenciamento de Serviços do Azure (`azure config mode asm`).

2. 	Descubra quais discos estão anexados à sua VM usando `azure vm disk list
	<virtual-machine-name>`:

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

3. 	Observe o LUN ou o **número de unidade lógica** para o disco que você deseja desanexar.


## Desanexar o disco

Depois de localizar o número LUN do disco, você estará pronto para desanexá-lo:

1. 	Desanexe o disco selecionado da máquina virtual executando o comando `azure vm disk detach
 	<virtual-machine-name> <LUN>`:

		$azure vm disk detach UbuntuVM 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	É possível verificar se o disco foi desanexado executando este comando:

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

O disco permanece desanexado no armazenamento mas já não está conectado a uma máquina virtual.

<!---HONumber=AcomDC_0608_2016-->