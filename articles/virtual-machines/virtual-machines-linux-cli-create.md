<properties
	pageTitle="Criar uma máquina virtual Linux | Microsoft Azure"
	description="Saiba como criar uma máquina virtual Linux ou uma máquina virtual Ubuntu usando uma imagem do Azure e a interface de linha de comando do Azure."
	keywords="máquina virtual linux, linux máquina virtual, máquina virtual ubuntu" 
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Criar uma máquina virtual Linux

É fácil criar uma máquina virtual (VM) Linux desde a linha de comando ou do portal. Este tutorial mostra como usar a interface de linha de comando do Azure (CLI) para Mac, Linux e Windows para criar rapidamente uma VM do Ubuntu Server em execução no Azure, conectar-se a ele usando **ssh** e criar e montar um novo disco. Este tópico usa uma VM do Ubuntu Server, mas é possível também criar uma máquina virtual do Linux usando [suas próprias imagens como modelos](virtual-machines-linux-classic-create-upload-vhd.md).

## Instalar a CLI do Azure

A primeira etapa é [instalar a CLI do Azure](../xplat-cli-install.md).

Muito bem. Agora, verifique se você está no modo do Gerenciador de Recursos ao digitar `azure config mode arm`.

Melhor ainda. [Faça logon com sua ID de trabalho ou escolar](../xplat-cli-connect.md#use-the-log-in-method) digitando `azure login` e seguindo as instruções para uma experiência de logon interativo para sua conta do Azure.

> [AZURE.NOTE] Se você tiver uma ID de trabalho ou escolar e se souber que não tem a autenticação de dois fatores habilitada, poderá usar `azure login -u` juntamente com a ID de trabalho ou escolar para efetuar logon sem uma sessão interativa. Se você não tiver uma ID de trabalho ou escolar, poderá [criar uma ID de trabalho ou escolar de sua conta da Microsoft pessoal](virtual-machines-windows-create-aad-work-id.md).

## Criar a máquina virtual Linux

Digite `azure group create <my-group-name> westus` substituindo _&lt;my-group-name&gt;_ por um nome de grupo exclusivo para você (pode usar uma região diferente, caso prefira). Você verá algo semelhante ao que se segue:

	azure group create myuniquegroupname westus
	info:    Executing command group create
	+ Getting resource group myuniquegroupname
	+ Creating resource group myuniquegroupname
	info:    Created resource group myuniquegroupname
	data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname
	data:    Name:                myuniquegroupname
	data:    Location:            westus
	data:    Provisioning State:  Succeeded
	data:    Tags:
	data:
	info:    group create command OK

Para criar uma VM, digite `azure vm quick-create` e você vai receber instruções para inserir os parâmetros restantes. Use o nome do grupo de recursos que você acabou de criar acima e, para o valor **ImageURN**, use `canonical:ubuntuserver:14.04.2-LTS:latest`, de modo que sua experiência seja semelhante à seguinte. Observe que o comando `azure vm quick-create` solicita as informações básicas necessárias para criar, hospedar e conectar-se a uma VM do Linux, incluindo:

- o nome do grupo de recursos e o nome da VM
- um local de implantação
- o tipo de sistema operacional e a cadeia de caracteres URN de imagem
- um nome de usuário e uma senha

e então cria a infraestrutura necessária para hospedar a VM. Isso inclui:

- Uma conta de armazenamento do Azure para armazenamento VHD e discos extras
- Uma NIC para a VM
- uma rede virtual com uma sub-rede
- um endereço IP público
- um subdomínio

		azure vm quick-create
		info:    Executing command vm quick-create
		Resource group name: myuniquegroupname
		Virtual machine name: myuniquevmname
		Location name: westus
		Operating system Type [Windows, Linux]: Linux
		ImageURN (format: "publisherName:offer:skus:version"): canonical:ubuntuserver:14.04.2-LTS:latest
		User name: ops
		Password: *********
		Confirm password: *********
		+ Looking up the VM "myuniquevmname"
		info:    Using the VM Size "Standard_D1"
		info:    The [OS, Data] Disk or image configuration requires storage account
		+ Retrieving storage accounts
		info:    Could not find any storage accounts in the region "westus", trying to create new one
		+ Creating storage account "cli3c0464f24f1bf4f014323" in "westus"
		+ Looking up the storage account cli3c0464f24f1bf4f014323
		+ Looking up the NIC "myuni-westu-1432328437727-nic"
		info:    An nic with given name "myuni-westu-1432328437727-nic" not found, creating a new one
		+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
		info:    Preparing to create new virtual network and subnet
		/ Creating a new virtual network "myuni-westu-1432328437727-vnet" [address prefix: "10.0.0.0/16"] with subnet "myuni-westu-1432328437727-snet"+[address prefix: "10.0.1.0/24"]
		+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
		+ Looking up the subnet "myuni-westu-1432328437727-snet" under the virtual network "myuni-westu-1432328437727-vnet"
		info:    Found public ip parameters, trying to setup PublicIP profile
		+ Looking up the public ip "myuni-westu-1432328437727-pip"
		info:    PublicIP with given name "myuni-westu-1432328437727-pip" not found, creating a new one
		+ Creating public ip "myuni-westu-1432328437727-pip"
		+ Looking up the public ip "myuni-westu-1432328437727-pip"
		+ Creating NIC "myuni-westu-1432328437727-nic"
		+ Looking up the NIC "myuni-westu-1432328437727-nic"
		+ Creating VM "myuniquevmname"
		+ Looking up the VM "myuniquevmname"
		+ Looking up the NIC "myuni-westu-1432328437727-nic"
		+ Looking up the public ip "myuni-westu-1432328437727-pip"
		data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Compute/virtualMachines/myuniquevmname
		data:    ProvisioningState               :Succeeded
		data:    Name                            :myuniquevmname
		data:    Location                        :westus
		data:    FQDN                            :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
		data:    Type                            :Microsoft.Compute/virtualMachines
		data:
		data:    Hardware Profile:
		data:      Size                          :Standard_D1
		data:
		data:    Storage Profile:
		data:      Image reference:
		data:        Publisher                   :canonical
		data:        Offer                       :ubuntuserver
		data:        Sku                         :14.04.2-LTS
		data:        Version                     :latest
		data:
		data:      OS Disk:
		data:        OSType                      :Linux
		data:        Name                        :cli3c0464f24f1bf4f0-os-1432328438224
		data:        Caching                     :ReadWrite
		data:        CreateOption                :FromImage
		data:        Vhd:
		data:          Uri                       :https://cli3c0464f24f1bf4f014323.blob.core.windows.net/vhds/cli3c0464f24f1bf4f0-os-1432328438224.vhd
		data:
		data:    OS Profile:
		data:      Computer Name                 :myuniquevmname
		data:      User Name                     :ops
		data:      Linux Configuration:
		data:        Disable Password Auth       :false
		data:
		data:    Network Profile:
		data:      Network Interfaces:
		data:        Network Interface #1:
		data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Network/networkInterfaces/myuni-westu-1432328437727-nic
		data:          Primary                   :true
		data:          MAC Address               :00-0D-3A-31-55-31
		data:          Provisioning State        :Succeeded
		data:          Name                      :myuni-westu-1432328437727-nic
		data:          Location                  :westus
		data:            Private IP alloc-method :Dynamic
		data:            Private IP address      :10.0.1.4
		data:            Public IP address       :191.239.51.1
		data:            FQDN                    :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
		info:    vm quick-create command OK

Sua VM está pronta para executar e aguardando a conexão.

## Conectar-se à máquina virtual Linux

Com as máquinas virtuais do Linux, normalmente você se conecta usando **ssh**.

> [AZURE.NOTE] Este tópico se refere a uma máquina virtual usando nomes de usuário e senhas. Para usar pares de chaves públicas e privadas para se comunicar com a VM, confira [Como usar uma SSH com Linux no Azure](virtual-machines-linux-ssh-from-linux.md). Você pode modificar a conectividade **SSH** de VMs criadas com o comando `azure vm quick-create` usando o comando `azure vm reset-access` para redefinir completamente o acesso **SSH**, adicionar ou remover usuários ou adicionar arquivos de chave pública para proteger o acesso. Este artigo usa um nome de usuário e uma senha com **SSH** por questões de brevidade.

Caso não esteja familiarizado com a conexão usando **SSH**, o comando assume a forma `ssh <username>@<publicdnsaddress> -p <the ssh port>`. Nesse caso, usamos o nome de usuário e a senha da etapa anterior e a porta 22, que é o padrão de porta **SSH**.

	ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
	The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
	ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
	ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Fri May 22 21:02:32 UTC 2015

	  System load: 0.37              Memory usage: 2%   Processes:       207
	  Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.



	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@myuniquevmname:~$

Agora que está conectado a uma VM, você está pronto para anexar um disco.

## Anexar e montar um disco

Anexar um novo disco é rápido. Basta digitar `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` para criar e anexar um novo disco GB a uma VM. O resultado deve ser semelhante a esse:

	azure vm disk attach-new myuniquegroupname myuniquevmname 5
	info:    Executing command vm disk attach-new
	+ Looking up the VM "myuniquevmname"
	info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
	+ Updating VM "myuniquevmname"
	info:    vm disk attach-new command OK


Agora, localize o disco usando `dmesg | grep SCSI` (o método usado para detectar o novo disco pode variar). Nesse caso, deve ser semelhante a:

	dmesg | grep SCSI
	[    0.294784] SCSI subsystem initialized
	[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
	[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
	[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
	[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk

e, no caso deste tópico, o disco do `sdc` é o que pretendemos. Agora, particione o disco com `sudo fdisk /dev/sdc`. Pressupondo que, no seu caso, o disco era `sdc`, torne-o um disco primário na partição 1 e aceite os outros padrões.

	sudo fdisk /dev/sdc
	Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
	Building a new DOS disklabel with disk identifier 0x2a59b123.
	Changes will remain in memory only, until you decide to write them.
	After that, of course, the previous content won't be recoverable.

	Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

	Command (m for help): n
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): p
	Partition number (1-4, default 1): 1
	First sector (2048-10485759, default 2048):
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
	Using default value 10485759

Para criar a partição, digite `p` no prompt:

	Command (m for help): p

	Disk /dev/sdc: 5368 MB, 5368709120 bytes
	255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x2a59b123

	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdc1            2048    10485759     5241856   83  Linux

	Command (m for help): w
	The partition table has been altered!

	Calling ioctl() to re-read partition table.
	Syncing disks.

Grave um sistema de arquivos para a partição usando o comando **mkfs**, especificando o tipo de sistema de arquivos e o nome do dispositivo. Nesse tópico, estamos usando o `ext4` e o `/dev/sdc1` acima:

	sudo mkfs -t ext4 /dev/sdc1
	mke2fs 1.42.9 (4-Feb-2014)
	Discarding device blocks: done
	Filesystem label=
	OS type: Linux
	Block size=4096 (log=2)
	Fragment size=4096 (log=2)
	Stride=0 blocks, Stripe width=0 blocks
	327680 inodes, 1310464 blocks
	65523 blocks (5.00%) reserved for the super user
	First data block=0
	Maximum filesystem blocks=1342177280
	40 block groups
	32768 blocks per group, 32768 fragments per group
	8192 inodes per group
	Superblock backups stored on blocks:
		32768, 98304, 163840, 229376, 294912, 819200, 884736

	Allocating group tables: done
	Writing inode tables: done
	Creating journal (32768 blocks): done
	Writing superblocks and filesystem accounting information: done

Agora, crie um diretório para montar o novo sistema de arquivos usando o `mkdir`:

	sudo mkdir /datadrive

E monte o diretório usando o `mount`:

	sudo mount /dev/sdc1 /datadrive

O disco de dados agora está pronto para uso como o `/datadrive`.

	ls
	bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
	boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var

> [AZURE.NOTE] Você também pode se conectar à máquina virtual Linux usando uma chave SSH para identificação. Para saber mais, confira [Como usar uma chave SSH com Linux no Azure](virtual-machines-linux-ssh-from-linux.md).

## Passo a passo em vídeo

Aqui está um passo a passo deste tutorial.

[AZURE.VIDEO building-a-linux-virtual-machine-tutorial]

## Próximas etapas

Lembre-se de que o novo disco normalmente não está disponível para a VM, caso seja reinicializado, a menos que você grave essas informações no seu arquivo [/etc/fstab](http://en.wikipedia.org/wiki/Fstab). Se desejar, você poderá adicionar vários outros discos e [configurar o RAID](virtual-machines-linux-configure-raid.md).

Para saber mais sobre o Linux no Azure, confira:

- [Computação Linux e Software Livre no Azure](virtual-machines-linux-opensource-links.md)

- [Como usar as ferramentas da interface de linha de comando do Azure](virtual-machines-command-line-tools.md)

- [Implantar um aplicativo LAMP usando a extensão CustomScript do Azure para Linux](virtual-machines-linux-classic-lamp-script.md)

- [A extensão de máquina virtual Docker para Linux no Azure](virtual-machines-linux-dockerextension.md)

<!---HONumber=AcomDC_0323_2016-->