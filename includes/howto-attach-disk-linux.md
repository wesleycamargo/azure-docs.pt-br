
Para obter mais detalhes sobre discos, consulte [Sobre discos e VHDs para máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md).

<a id="attachempty"></a>

## Anexar um disco vazio
1. Abra a CLI do Azure e [conecte-se à sua assinatura do Azure](../articles/xplat-cli-connect.md). Verifique se você está no modo de Gerenciamento de Serviços do Azure (`azure config mode asm`).
2. Insira `azure vm disk attach-new` para criar e anexar um novo disco, conforme mostrado abaixo. Substitua *TestVM* pelo nome da máquina virtual Linux e especifique o tamanho do disco em GB, que é de 100 GB neste exemplo:
   
       azure vm disk attach-new TestVM 100
3. Após o disco de dados ser criado e anexado, ele é listado no resultado do `azure vm disk list <virtual-machine-name>`, da seguinte forma:
   
     $ azure vm disk list TestVM
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK

<a id="attachexisting"></a>

## Anexar um disco existente
Anexar um disco existente exige que você tenha um .vhd disponível em uma conta de armazenamento.

1. Abra a CLI do Azure e [conecte-se à sua assinatura do Azure](../articles/xplat-cli-connect.md). Verifique se você está no modo de Gerenciamento de Serviços do Azure (`azure config mode asm`).
2. Verifique se o VHD que você deseja anexar já está carregado à sua assinatura do Azure:
   
     $azure vm disk list
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
3. Se você não encontrar o disco que deseja usar, você pode carregar um VHD local na sua assinatura usando `azure vm disk create` ou `azure vm disk upload`. Um exemplo de `disk create` seria como no seguinte:
   
       $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
       info:    Executing command vm disk create
       + Retrieving storage accounts
       info:    VHD size : 10 GB
       info:    Uploading 10485760.5 KB
       Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
       info:    Finishing computing MD5 hash, 16% is complete.
       info:    https://mystorageaccount.blob.core.windows.net/disks/test.VHD was
       uploaded successfully
       info:    vm disk create command OK
   
   Você também pode usar `azure vm disk upload` para carregar um VHD em uma conta de armazenamento específico. Leia mais sobre os comandos para gerenciar seus discos de dados de máquinas virtuais do Azure [aqui](../articles/virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks).
4. Agora você anexa o VHD desejado à sua máquina virtual:
   
       $azure vm disk attach TestVM myTestVhd
       info:    Executing command vm disk attach
       + Getting virtual machines
       + Adding Data-Disk
       info:    vm disk attach command OK
   
   Certifique-se de substituir *TestVM* com o nome da sua máquina virtual, e *myTestVhd* com seu VHD desejado.
5. Você pode verificar se o disco está anexado à máquina virtual com `azure vm disk list <virtual-machine-name>`:
   
     $azure vm disk list TestVM
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK

> [!NOTE]
> Depois de adicionar um disco de dados, será necessário fazer logon na máquina virtual e inicializar o disco para que a máquina virtual possa usar o disco para armazenamento (consulte as etapas abaixo para obter mais informações sobre como fazer isso).
> 
> 

