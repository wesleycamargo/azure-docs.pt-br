<properties
	pageTitle="Como usar o Armazenamento de arquivos do Azure com o Linux | Microsoft Azure"
        description="Criar um compartilhamento de arquivos na nuvem e montá-lo de uma VM do Azure ou um aplicativo local em execução no Linux."
        services="storage"
        documentationCenter="na"
        authors="jutang"
        manager="jahogg"
        editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="09/28/2015"
      ms.author="jutang;tamram" />


# Como usar o Armazenamento de Arquivos do Azure com o Linux 

## Visão geral

O Armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão. O Armazenamento de arquivos agora está disponível e dá suporte ao SMB 3.0 e 2.1.

É possível criar compartilhamentos de arquivos do Azure usando o portal de visualização do Azure, os cmdlets do PowerShell do Armazenamento do Azure, as bibliotecas de cliente do Armazenamento do Azure ou a API REST do Armazenamento do Azure. Além disso, como os compartilhamentos de arquivos são compartilhamentos do SMB, você pode acessá-los por meio de APIs padrão e clássicas do sistema de arquivos.

Os aplicativos em execução no Azure podem facilmente montar compartilhamentos de arquivos por meio das máquinas virtuais do Azure. E com a versão mais recente do Armazenamento de arquivos, também é possível montar um compartilhamento de arquivos por meio de um aplicativo local que dá suporte ao SMB 3.0.

É importante lembrar que já que o cliente SMB do Linux ainda não dá suporte à criptografia, montar um compartilhamento de arquivos do Linux ainda requer que o cliente esteja na mesma região do Azure que o compartilhamento de arquivos. No entanto, o suporte à criptografia para Linux está no roteiro dos desenvolvedores do Linux responsáveis pela funcionalidade SMB. As distribuições do Linux que dão suporte à criptografia no futuro também poderão montar um Compartilhamento de arquivos do Azure em qualquer lugar.

## Qual distribuição do Linux deve ser usada? ##

Ao criar uma máquina virtual do Linux no Azure, é possível especificar uma imagem do Linux que dá suporte ao SMB 2.1 ou posterior por meio da galeria de imagens do Azure. Veja abaixo uma lista das imagens do Linux recomendadas:

- Ubuntu Server 14.04	
- Ubuntu Server 15.04	
- CentOS 7.1	
- Open SUSE 13.2	
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 (Premium Image)

## Montar o compartilhamento de arquivos ##

Para montar o compartilhamento de arquivos por meio de uma máquina virtual que executa o Linux, você pode precisar instalar um cliente SMB/CIFS se a distribuição que está sendo usada não tiver um cliente interno. Este é o comando do Ubuntu para instalar uma opção cifs-utils:

    sudo apt-get install cifs-utils

Em seguida, você precisa fazer um ponto de montagem (mkdir mymountpoint) e emitir o comando mount que seja semelhante a este:

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Você também pode adicionar configurações em /etc/fstab para montar o compartilhamento.

Lembre-se de que 0777 aqui representa um código de permissão de arquivo/diretório que fornece permissões de execução/leitura/gravação para todos os usuários. Você pode substituí-lo por outro código de permissão do arquivo seguindo o documento de permissão de arquivo do Linux.
 
Também para manter um compartilhamento de arquivos montado após a reinicialização, é possível adicionar uma configuração como a abaixo em /etc/fstab:

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username= myaccountname,password= StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Para ser mais específico, veja um exemplo abaixo.

Se você criou uma VM do Azure usando a imagem Ubuntu Server 15.04 do Linux que está disponível no Azure Marketplace, é possível montar o arquivo como mostrado abaixo:

    azureuser@azureconubuntu:~$ sudo apt-get install apt-file
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Se você usar o CentOS 7.1, é possível montar o arquivo como mostrado abaixo:

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Se você usar o Open SUSE 13.2, você pode montar o arquivo como mostrado abaixo:

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint


## Próximas etapas

Consulte estes links para obter mais informações sobre o armazenamento de arquivo do Azure.

### Artigos conceituais

- [Como usar o Armazenamento de Arquivos do Azure com o Windows](storage-dotnet-how-to-use-files.md)

### Suporte de ferramentas para o Armazenamento de arquivos

- [Como usar o AzCopy com o Armazenamento do Microsoft Azure](storage-use-azcopy.md)
- [Usando a CLI do Azure com o Armazenamento do Azure](storage-azure-cli.md#create-and-manage-file-shares)

### Referência

- [Referência à API REST do serviço de arquivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Postagens no blog

- [O Armazenamento de arquivos do Azure agora está disponível ao público geral](http://go.microsoft.com/fwlink/?LinkID=626728&clcid=0x409)
- [Aprofunde-se com o Armazenamento de arquivos do Azure](http://go.microsoft.com/fwlink/?LinkID=626729&clcid=0x409) 
- [Apresentando o serviço de arquivo do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Persistindo conexões para arquivos do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=Oct15_HO1-->