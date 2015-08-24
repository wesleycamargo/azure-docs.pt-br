
Para obter mais detalhes sobre discos, consulte [Sobre discos e VHDs para máquinas virtuais](../articles/virtual-machines-disks-vhds.md).

<a id="attachempty"></a>
## Como: anexar um disco vazio
Anexar um disco vazio é o modo mais simples de se adicionar um disco de dados, porque o Azure cria o arquivo .vhd para você e o coloca na conta de armazenamento.

1.  Abra a CLI do Azure para Mac, Linux, e Windows e conecte-se à sua assinatura do Azure. Consulte [Conectar ao Azure da CLI do Azure](../articles/xplat-cli-connect.md) para obter mais informações.

2.  Verifique se você está no modo de gerenciamento de serviços do Azure, que é o padrão digitando `azure config
 	mode asm`.

3.  Use o comando `azure vm disk attach-new` para criar e anexar um novo disco, conforme mostrado abaixo. Observe que o _ubuntuVMasm_ será substituído pelo nome da VM da máquina virtual do Linux que você criou na sua assinatura. O número 30 é o tamanho do disco em GB neste exemplo.

        azure vm disk attach-new ubuntuVMasm 30

4.	Após o disco de dados ser criado e anexado, ele é listado no resultado do `azure vm disk list
    <virtual-machine-name>`, da seguinte forma:

        $ azure vm disk list ubuntuVMasm
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
        data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## Como: anexar um disco existente

Anexar um disco existente exige que você tenha um .vhd disponível em uma conta de armazenamento.

1. 	Abra a CLI do Azure para Mac, Linux, e Windows e conecte-se à sua assinatura do Azure. Consulte [Conectar ao Azure da CLI do Azure](../articles/xplat-cli-connect.md) para obter mais informações.

2.  Verifique se você está no modo de gerenciamento de serviços do Azure, que é o padrão. Se você tiver alterado o modo para gerenciamento de recursos, simplesmente reverta digitando `azure config mode asm`.

3.	Descubra se o VHD que você deseja anexar já está carregado à sua assinatura do Azure usando:

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060029150744  Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

4.  Se você não encontrar o disco que deseja usar, você pode carregar um VHD local na sua assinatura usando `azure vm disk create` ou `azure vm disk upload`. Um exemplo disso seria:

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://portalvhdsq1s6mc7mqf4gn.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	Você também pode usar o comando `azure vm disk upload` para carregar um VHD em uma conta de armazenamento específico. Leia mais sobre os comandos para gerenciar seus discos de dados de máquinas virtuais do Azure [aqui](../virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks).

5.  Digite o seguinte comando para anexar o VHD carregado desejado na máquina virtual:

		$azure vm disk attach ubuntuVMasm myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	Certifique-se de substituir _ubuntuVMasm_ com o nome da sua máquina virtual, e _myTestVhd_ com seu VHD desejado.

6.	Você pode verificar se o disco está anexado à máquina virtual com o comando `azure vm disk list
 	<virtual-machine-name>` como:

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK


> [AZURE.NOTE]Depois de adicionar um disco de dados, você precisará fazer logon na máquina virtual e inicializar o disco para que a máquina virtual possa usar o disco para armazenamento.

<!---HONumber=August15_HO7-->