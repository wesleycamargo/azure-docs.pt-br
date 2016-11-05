
# Como desanexar um disco de dados de uma máquina virtual com o CLI
Quando você não precisar mais um disco de dados que está conectado a uma máquina virtual, você pode desconectá-lo facilmente. Essa ação remove o disco da máquina virtual, mas não o remove do armazenamento. Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.

> [!NOTE]
> Uma máquina virtual no Azure usa diferentes tipos de discos: um disco de sistema operacional, um disco temporário local e discos de dados opcionais. Discos de dados são a forma recomendada de armazenar dados para uma máquina virtual. Para obter detalhes sobre discos, consulte [Sobre discos e imagens](http://go.microsoft.com/fwlink/p/?LinkId=263439). Não é possível desanexar um disco do sistema operacional neste momento.
> 
> 

1. Obtenha a lista de discos anexados à sua VM:
   
        vm disk list <vm-name>
   
    Se você omitir `<vm-name>`, você obterá uma lista de todos os discos em sua assinatura.
2. Desanexe um disco:
   
        vm disk detach <vm-name> <lun>
   
    `lun` identifica o disco a ser desanexado e será um número que pode ser encontrado na lista de disco da VM.

Exemplo de passo a passo incluindo saída do terminal:

    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    data:    2    7         kmlinux-602362868dbb7439.vhd
    info:    vm disk list command OK
    ~$ azure vm disk detach kmlinux 2
    info:    Executing command vm disk detach
    + Getting virtual machines
    + Removing Data-Disk
    info:    vm disk detach command OK
    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=August15_HO6-->