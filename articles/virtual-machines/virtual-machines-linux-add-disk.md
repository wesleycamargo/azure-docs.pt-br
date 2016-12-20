---
title: Adicionar um disco a uma VM do Linux | Microsoft Docs
description: "Aprenda a adicionar um disco persistente à sua VM do Linux"
keywords: "máquina virtual do Linux, adicionar disco de recurso"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 3005a066-7a84-4dc5-bdaa-574c75e6e411
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.date: 09/06/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 9baff59be09c168b31ec78ccdb58c4b8b26de274


---
# <a name="add-a-disk-to-a-linux-vm"></a>Adicionar um disco a uma VM do Linux
Este artigo mostra como anexar um disco persistente à sua VM para que você possa preservar dados, mesmo que sua VM seja provisionada novamente devido à manutenção ou ao redimensionamento. Para adicionar um disco, você precisa da [CLI do Azure](../xplat-cli-install.md) configurada no modo Resource Manager (`azure config mode arm`).  

## <a name="quick-commands"></a>Comandos rápidos
O exemplo a seguir anexa um disco de `50` GB à VM denominada `myVM` no grupo de recursos denominado `myResourceGroup`:

```azurecli
azure vm disk attach-new myResourceGroup myVM 50
```

## <a name="attach-a-disk"></a>Anexar um disco
Anexar um novo disco é rápido. Digite `azure vm disk attach-new myResourceGroup myVM sizeInGB` para criar e anexar um novo disco GB à sua VM. Se você não identificar explicitamente uma conta de armazenamento, qualquer disco que você criar será colocado na mesma conta de armazenamento na qual o disco do sistema operacional reside. O exemplo a seguir anexa um disco de `50` GB à VM denominada `myVM` no grupo de recursos denominado `myResourceGroup`:

```azurecli
azure vm disk attach-new myResourceGroup myVM 50
```

Saída

```azurecli
info:    Executing command vm disk attach-new
+ Looking up the VM "myVM"
info:    New data disk location: https://mystorageaccount.blob.core.windows.net/vhds/myVM-20150526-043.vhd
+ Updating VM "myVM"
info:    vm disk attach-new command OK
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Conectar-se à VM do Linux para montar o novo disco
> [!NOTE]
> Este tópico faz a conexão com uma VM usando nomes de usuário e senhas. Para usar pares de chaves pública e privada a fim de se comunicar com a VM, confira [Como usar SSH com Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Você pode modificar a conectividade **SSH** de VMs criadas com o comando `azure vm quick-create` usando o comando `azure vm reset-access` para redefinir completamente o acesso **SSH**, adicionar ou remover usuários ou adicionar arquivos de chave pública para proteger o acesso.
> 
> 

Você precisa de SSH em sua VM do Azure para particionar, formatar e montar o novo disco para que sua VM do Linux possa usá-lo. Se você não estiver familiarizado com a conexão usando **ssh**, o comando assumirá a forma `ssh <username>@<FQDNofAzureVM> -p <the ssh port>` e será semelhante ao seguinte:

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com -p 22
```

Saída

```bash
The authenticity of host 'mypublicdns.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@mypublicdns.westus.cloudapp.azure.com's password:
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

ops@myVM:~$
```

Agora que está conectado a uma VM, você está pronto para anexar um disco.  Primeiro, localize o disco usando `dmesg | grep SCSI` (o método usado para descobrir o novo disco pode variar). Nesse caso, deve ser semelhante a:

```bash
dmesg | grep SCSI
```

Saída

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

e, no caso deste tópico, o disco do `sdc` é o que pretendemos. Agora, particione o disco com `sudo fdisk /dev/sdc`. Pressupondo que, no seu caso, o disco era `sdc`, torne-o um disco primário na partição 1 e aceite os outros padrões.

```bash
sudo fdisk /dev/sdc
```

Saída

```bash
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
```

Para criar a partição, digite `p` no prompt:

```bash
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
```

Grave um sistema de arquivos para a partição usando o comando **mkfs** , especificando o tipo de sistema de arquivos e o nome do dispositivo. Nesse tópico, estamos usando o `ext4` e o `/dev/sdc1` acima:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Saída

```bash
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
```

Agora, crie um diretório para montar o novo sistema de arquivos usando o `mkdir`:

```bash
sudo mkdir /datadrive
```

E monte o diretório usando o `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

O disco de dados agora está pronto para uso como o `/datadrive`.

```bash
ls
```

Saída

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

Para garantir que a unidade seja remontada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo /etc/fstab. Além disso, é altamente recomendável que o UUID (Identificador Universal Exclusivo) seja usado no /etc/fstab para fazer referência à unidade, e não apenas ao nome do dispositivo (por exemplo, `/dev/sdc1`). Se o sistema operacional detectar um erro de disco durante a inicialização, usar o UUID evita que o disco incorreto seja montado em um determinado local. Os discos de dados restantes seriam então atribuídos a essas mesmas IDs de dispositivo. Para localizar o UUID da nova unidade, use o utilitário **blkid** :

```bash
sudo -i blkid
```

A saída deve ser semelhante a esta:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> A edição inadequada do arquivo **/etc/fstab** pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.
> 
> 

Em seguida, abra o arquivo **/etc/fstab** em um editor de texto:

```bash
sudo vi /etc/fstab
```

Neste exemplo, usamos o valor UUID para o novo dispositivo **/dev/sdc1** criado nas etapas anteriores e o ponto de montagem **/datadrive**. Adicione a seguinte linha no final do arquivo **/etc/fstab** :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Remover um disco de dados posteriormente sem editar fstab pode fazer com que a VM falhe ao ser inicializada. A maioria das distribuições fornece as opções fstab `nofail` e/ou `nobootwait`. Essas opções permitem que um sistema inicialize mesmo se o disco não for montado no momento da inicialização. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.
> 
> A opção **nofail** garante que a VM inicie mesmo que o sistema de arquivos esteja corrompido ou que o disco não exista no momento da inicialização. Sem essa opção, você poderá encontrar um comportamento conforme descrito em [Não é possível conectar-se a uma VM Linux via SSH devido a erros no FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte a TRIM/UNMAP para Linux no Azure
Alguns kernels Linux permitem operações TRIM/UNMAP para descartar os blocos não utilizados no disco. Isso é útil principalmente no Armazenamento Standard, para informar o Azure de que as páginas excluídas não são mais válidas e podem ser descartadas. Isso poderá representar uma economia de dinheiro se você criar arquivos grandes e, em seguida, excluí-los.

Há duas maneiras de habilitar o suporte a TRIM em sua VM do Linux. Como de costume, consulte sua distribuição para obter a abordagem recomendada:

* Use a opção de montagem `discard` em `/etc/fstab`, por exemplo:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Como alternativa, você pode executar o comando `fstrim` manualmente na linha de comando ou adicioná-lo a crontab para ser executado normalmente:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Solucionar problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Próximas etapas
* Lembre-se de que o novo disco não está disponível para a VM, caso seja reinicializado, a menos que você grave essas informações no seu arquivo [fstab](http://en.wikipedia.org/wiki/Fstab) .
* Para garantir que a VM Linux seja configurada corretamente, leia as recomendações em [Otimizar sua VM do Linux no Azure](virtual-machines-linux-optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
* Expanda a capacidade de armazenamento adicionando mais discos e [configure o RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter desempenho adicional.




<!--HONumber=Nov16_HO3-->


