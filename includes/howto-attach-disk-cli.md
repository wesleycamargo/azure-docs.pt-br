
Para mais informações sobre discos, consulte [Sobre discos da máquina virtual no Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

## <a id="cliattachempty"></a>Tutorial: anexar um disco vazio
Anexar um disco vazio é a maneira mais simples de adicionar um disco de dados. Execute o seguinte comando para anexar um novo disco vazio:

    vm disk attach-new <vm-name> <size-in-gb> [blob-url]

Substitua `vm-name` pelo nome da máquina virtual e `size-in-gb` pelo tamanho do seu novo disco. Opcionalmente, você pode usar uma URL de blob como o último argumento para especificar explicitamente o blob de destino a ser criado. Se você não especificar uma URL de blob, um objeto blob será gerado automaticamente.

Execute o seguinte comando para verificar se o disco foi criado:

    vm disk list <vm-name>

Aqui está um exemplo passo a passo dos comandos acima incluindo a saída de terminal:

    ~$ azure vm disk attach-new pinkylinux 20 http://pinkylinux.blob.core.windows.net/vhds/pinkydisk1.vhd
    info:   Executing command vm disk attach-new
    + Getting virtual machines
    + Adding Data-Disk
    info:   vm disk attach-new command OK
    ~$ azure vm disk list pinkylinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        pinkylinux-pinkylinux-2015-02-05.vhd    Linux
    data:    0    5         pinkydisk1.vhd
    data:    1    20        pinkylinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=August15_HO6-->