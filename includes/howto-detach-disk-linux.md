Quando não precisar mais de um disco de dados conectado a uma VM (máquina virtual), você poderá desanexá-lo facilmente. Essa ação remove o disco da VM, mas não o remove do armazenamento. Se desejar usar os dados existentes no disco novamente, você poderá reanexá-lo à mesma VM ou anexá-lo a outra.

> [AZURE.NOTE] Uma VM no Azure usa diferentes tipos de discos, um disco de sistema operacional, um disco temporário local e discos de dados opcionais. Para obter detalhes, consulte [Sobre discos e VHDs para Máquinas Virtuais](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md). Não é possível desanexar um disco do sistema operacional, a menos que você também exclua a VM.


## Localizar o disco

Antes de poder desanexar um disco de uma VM, você precisa descobrir o número LUN, que é um identificador para o disco a ser desanexado. Para fazer isso, siga estas etapas:

1. 	Abra a CLI do Azure e [conecte-se à sua assinatura do Azure](../articles/xplat-cli-connect.md). Verifique se você está no modo de Gerenciamento de Serviços do Azure (`azure config mode asm`).

2. 	Descubra quais discos estão anexados à sua VM usando `azure vm disk list <virtual-machine-name>`:

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

## Remover as referências do sistema operacional no disco

Antes de desanexar o disco do convidado do Linux, você deve se certificar de que nenhuma das partições no disco está em uso. Certifique-se de que o sistema operacional não tente remontá-los após uma reinicialização. Essas etapas desfazem a configuração que você provavelmente criou ao [anexar](../articles/virtual-machines/virtual-machines-linux-classic-attach-disk.md) o disco.

1. Use o comando `lsscsi` para descobrir o identificador do disco. `lsscsi` pode ser instalada pelo `yum install lsscsi` (em distribuições baseadas em Red Hat) ou pelo `apt-get install lsscsi` (em distribuições baseadas em Debian). Você pode encontrar o identificador de disco que está procurando usando o número de LUN. O último número na tupla em cada linha é o LUN. No exemplo a seguir, LUN 0 é mapeado para _/dev/sdc_

			ops@TestVM:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
			[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd

2. Use `fdisk -l <disk>` para descobrir as partições associadas ao disco a ser desanexado.
3. 
			$ sudo fdisk -l /dev/sdc
			Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
			Units = sectors of 1 * 512 = 512 bytes
			Sector size (logical/physical): 512 bytes / 512 bytes
			I/O size (minimum/optimal): 512 bytes / 512 bytes
			Disk label type: dos
			Disk identifier: 0x5a1d2a1a

			   Device Boot      Start         End      Blocks   Id  System
			/dev/sdc1            2048  2145386495  1072692224   83  Linux

3. Desmonte cada partição listada para o disco. Neste exemplo: `$ sudo umount /dev/sdc1`
4. Use o comando `blkid` para descobrir os UUIDs de todas as partições

			$ sudo blkid
			/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
			/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
			/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
			/dev/sdd1: UUID="44444444-4b4b-4c4c-4d4d-4e4e4e4e4e4e" TYPE="ext4
			
5. Remova entradas no arquivo **/etc/fstab** associado aos caminhos de dispositivo ou UUIDs para todas as partições do disco a ser desanexado. As entradas para este exemplo poderiam ser:

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
ou o

		/dev/sdc1   /datadrive   ext4   defaults   1   2


## Desanexar o disco

Depois de localizar o número de LUN do disco e remover as referências do sistema operacional, você está pronto para desanexá-lo:

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

<!---HONumber=AcomDC_0824_2016-->