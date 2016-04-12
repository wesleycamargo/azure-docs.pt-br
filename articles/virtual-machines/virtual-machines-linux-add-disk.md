<properties
	pageTitle="Adicionar um disco a uma VM do Linux | Microsoft Azure"
	description="Aprenda a adicionar um disco persistente à sua VM do Linux"
	keywords="máquina virtual do Linux, adicionar disco de recurso"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rickstercdn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.topic="get-started-article"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.date="03/01/2016"
	ms.author="rclaus"/>

# Adicionar um disco a uma VM do Linux

Este artigo mostra como anexar um disco persistente à sua VM para que você possa preservar seus dados, mesmo que sua máquina virtual seja provisionada novamente devido a manutenção ou redimensionamento. Para adicionar um disco, você precisará da [CLI do Azure](../xplat-cli-install.md) no modo do gerenciador de recursos (`azure config mode arm`).

## Comandos rápidos

```
# In the following command examples, replace the values between &lt; and &gt; with the values from your own environment.

rick@ubuntu$ azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## Anexar um disco

Anexar um novo disco é rápido. Basta digitar `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` para criar e anexar um novo disco GB a uma VM. O resultado deve ser semelhante a esse:

	azure vm disk attach-new myuniquegroupname myuniquevmname 5
	info:    Executing command vm disk attach-new
	+ Looking up the VM "myuniquevmname"
	info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
	+ Updating VM "myuniquevmname"
	info:    vm disk attach-new command OK


## Conectar-se à VM do Linux para montar o novo disco

> [AZURE.NOTE] Este tópico se refere a uma máquina virtual usando nomes de usuário e senhas. Para usar pares de chaves públicas e privadas para se comunicar com a VM, confira [Como usar uma SSH com Linux no Azure](virtual-machines-linux-ssh-from-linux.md). Você pode modificar a conectividade **SSH** de VMs criadas com o comando `azure vm quick-create` usando o comando `azure vm reset-access` para redefinir completamente o acesso **SSH**, adicionar ou remover usuários ou adicionar arquivos de chave pública para proteger o acesso.

Você precisará de SSH em sua VM do Azure para particionar, formatar e montar o novo disco para que sua VM do Linux possa usá-lo. Se você não estiver familiarizado com a conexão usando **ssh**, o comando assumirá a forma `ssh <username>@<FQDNofAzureVM> -p <the ssh port>` e parecerá com o seguinte.

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

Agora que está conectado a uma VM, você está pronto para anexar um disco. Agora, localize o disco usando `dmesg | grep SCSI` (o método usado para detectar o novo disco pode variar). Nesse caso, deve ser semelhante a:

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

## Próximas etapas

- Lembre-se de que o novo disco normalmente não está disponível para a VM, caso seja reinicializado, a menos que você grave essas informações no seu arquivo [/etc/fstab](http://en.wikipedia.org/wiki/Fstab).
- Examine as recomendações de [Otimizar o desempenho do computador Linux](virtual-machines-linux-optimization.md) para assegurar que sua VM do Linux esteja configurada corretamente.
- Expanda a capacidade de armazenamento ao adicionar outros discos e [configurar o RAID](virtual-machines-linux-configure-raid.md) para obter desempenho adicional.

<!---HONumber=AcomDC_0406_2016-->