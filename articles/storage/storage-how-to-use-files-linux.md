---
title: Como usar os Arquivos do Azure com o Linux | Microsoft Docs
description: "Crie um compartilhamento de arquivos do Azure na nuvem com este tutorial passo a passo. Gerencie o conteúdo do compartilhamento de arquivos e monte um compartilhamento de arquivos de uma máquina virtual do Azure (VM) ou de um aplicativo local que suporta SMB 3.0."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/8/2017
ms.author: renash
translationtype: Human Translation
ms.sourcegitcommit: e296e468309b53338231e283ac62e4d917e0834b
ms.openlocfilehash: 8cb98eb721d5769125926a6c75f776a9d510376e
ms.lasthandoff: 01/18/2017


---
# <a name="how-to-use-azure-file-storage-with-linux"></a>Como usar o Armazenamento de Arquivos do Azure com o Linux
## <a name="overview"></a>Visão geral
O armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão. Com os Arquivos do Azure, você pode migrar para o Azure os aplicativos empresariais que dependam de servidores de arquivos. Os aplicativos em execução no Azure podem facilmente montar compartilhamentos de arquivos a partir das máquinas virtuais do Azure executando o Linux. E com a versão mais recente do Armazenamento de arquivos, também é possível montar um compartilhamento de arquivos por meio de um aplicativo local que dá suporte ao SMB 3.0.

Você pode criar compartilhamentos de arquivos do Azure usando o [Portal do Azure](https://portal.azure.com), os cmdlets do PowerShell do Armazenamento do Azure, as bibliotecas de cliente do Armazenamento do Azure ou a API REST do Armazenamento do Azure. Além disso, como os compartilhamentos de arquivos são compartilhamentos do SMB, você pode acessá-los por meio de APIs padrão do sistema de arquivos.

O armazenamento de arquivos baseia-se na mesma tecnologia de armazenamento de Blobs, Tabelas e Filas e, portanto, o Armazenamento de arquivos oferece a disponibilidade, a durabilidade, a escalabilidade e a redundância geográfica existentes e incorporadas à plataforma de armazenamento do Azure. Para obter detalhes os destinos e os limites do desempenho do Armazenamento de arquivos, veja [Escalabilidade e metas de desempenho do Armazenamento do Azure](storage-scalability-targets.md).

O armazenamento de arquivo agora está disponível e dá suporte a SMB 2.1 e a SMB 3.0. Para obter detalhes adicionais sobre o Armazenamento de arquivos, veja a [API REST do serviço de arquivo](https://msdn.microsoft.com/library/azure/dn167006.aspx).

> [!NOTE]
> O cliente SMB do Linux ainda não dá suporte à criptografia, portanto, montar um compartilhamento de arquivos do Linux ainda requer que o cliente esteja na mesma região do Azure que o compartilhamento de arquivos. No entanto, o suporte à criptografia para Linux está no roteiro dos desenvolvedores do Linux responsáveis pela funcionalidade SMB. As distribuições do Linux que dão suporte à criptografia no futuro também poderão montar um Compartilhamento de arquivos do Azure em qualquer lugar.
> 
> 

## <a name="video-using-azure-file-storage-with-linux"></a>Vídeo: Usando o armazenamento de arquivos do Azure com o Linux
Veja um vídeo que demonstra como criar e usar compartilhamentos de arquivos do Azure no Linux.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-File-Storage-with-Linux/player]
> 
> 

## <a name="choose-a-linux-distribution-to-use"></a>Escolher uma distribuição do Linux a ser usada
Ao criar uma máquina virtual do Linux no Azure, é possível especificar uma imagem do Linux que dá suporte ao SMB 2.1 ou posterior por meio da galeria de imagens do Azure. Veja abaixo uma lista das imagens do Linux recomendadas:

* Ubuntu Server 14.04+
* RHEL 7+
* CentOS 7+
* Debian 8
* openSUSE 13.2+
* SUSE Linux Enterprise Server 12
* SUSE Linux Enterprise Server 12 (Premium Image)

## <a name="mount-the-file-share"></a>Montar o compartilhamento de arquivos
Para montar o compartilhamento de arquivos por meio de uma máquina virtual que executa o Linux, você pode precisar instalar um cliente SMB/CIFS se a distribuição que está sendo usada não tiver um cliente interno. Este é o comando do Ubuntu para instalar uma opção cifs-utils:

```
sudo apt-get install cifs-utils
```

Em seguida, você precisa fazer um ponto de montagem (mkdir mymountpoint) e emitir o comando mount que seja semelhante a este:

```
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777,serverino
```

Você também pode adicionar configurações em /etc/fstab para montar o compartilhamento.

Lembre-se de que 0777 aqui representa um código de permissão de arquivo/diretório que fornece permissões de execução/leitura/gravação para todos os usuários. Você pode substituí-lo por outro código de permissão do arquivo seguindo o documento de permissão de arquivo do Linux.

Também para manter um compartilhamento de arquivos montado após a reinicialização, é possível adicionar uma configuração como a abaixo em /etc/fstab:

```
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777,serverino
```

Por exemplo, se você criou uma VM do Azure usando a imagem Ubuntu Server 15.04 do Linux (que está disponível na galeria de imagens do Azure), é possível montar o arquivo como mostrado abaixo:

```
azureuser@azureconubuntu:~$ sudo apt-get install cifs-utils
azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777,serverino
azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
Filesystem  Size  Used Avail Use% Mounted on
//myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint
```

Se você usar o CentOS 7.1, é possível montar o arquivo como mostrado abaixo:

```
[azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
[azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777,serverino
[azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
Filesystem  Size  Used Avail Use% Mounted on
//myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint
```

Se você usar o Open SUSE 13.2, você pode montar o arquivo como mostrado abaixo:

```
azureuser@AzureconSuse:~> sudo zypper install samba*  
azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777,serverino
azureuser@AzureconSuse:~> df -h /mnt/mountpoint
Filesystem  Size  Used Avail Use% Mounted on
//myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint
```

Se você usar o RHEL 7.3, será possível montar o arquivo como mostrado abaixo:

```
azureuser@AzureconRedhat:~> sudo yum install cifs-utils
azureuser@AzureconRedhat:~> sudo mkdir /mnt/mountpoint
azureuser@AzureconRedhat:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777,serverino
azureuser@AzureconRedhat:~> df -h /mnt/mountpoint
Filesystem  Size  Used Avail Use% Mounted on
//myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint
```

## <a name="manage-the-file-share"></a>Gerenciar o compartilhamento de arquivos
O [portal do Azure](https://portal.azure.com) fornece uma interface do usuário para gerenciar o Armazenamento de Arquivos do Azure. Você pode executar as seguintes ações em seu navegador da Web:

* Carregar e baixar arquivos de e para o compartilhamento de arquivos.
* Monitorar o uso real de cada compartilhamento de arquivos.
* Ajustar a cota de tamanho do compartilhamento de arquivos.
* Copiar o comando `net use` a ser usado para montar o compartilhamento de arquivos de um cliente do Windows.

Você também pode usar a CLI do Azure (Interface de Linha de Comando de Plataforma Cruzada do Azure) do Linux para gerenciar o compartilhamento de arquivos. A CLI do Azure fornece um conjunto de comandos de software livre e de plataforma cruzada para trabalhar com o Armazenamento do Azure, incluindo o Armazenamento de arquivos. Ela fornece grande parte das mesmas funcionalidades encontradas no Portal do Azure, bem como funcionalidades avançadas de acesso a dados. Para obter exemplos, veja [Usando a CLI do Azure com o Armazenamento do Azure](storage-azure-cli.md).

## <a name="develop-with-file-storage"></a>Desenvolver com o armazenamento de arquivo
Como desenvolvedor, você pode compilar um aplicativo com o Armazenamento de arquivos usando a [Biblioteca de Cliente do Armazenamento do Azure para Java](https://github.com/azure/azure-storage-java). Para obter exemplos de código, confira [Como usar o Armazenamento de arquivos por meio do Java](storage-java-how-to-use-file-storage.md).

Você também pode usar a [Biblioteca de Cliente do Armazenamento do Azure para Node.js](https://github.com/Azure/azure-storage-node) para desenvolver um Armazenamento de arquivos.

## <a name="feedback-and-more-information"></a>Comentários e mais informações
Usuários do Linux, queremos ouvir sua opinião!

O Armazenamento de arquivos do Azure para o grupo de usuários do Linux oferece um fórum para que você possa compartilhar comentários à medida que você avalia e adota o Armazenamento de arquivos no Linux. Envie um email [aos usuários do Linux de Armazenamento de Arquivos do Azure](mailto:azurefileslinuxusers@microsoft.com) para participar do grupo de usuários.

## <a name="next-steps"></a>Próximas etapas
Consulte estes links para obter mais informações sobre o armazenamento de arquivo do Azure.

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos conceituais
* [Armazenamento de Arquivos do Azure: um sistema de arquivos SMB de nuvem ininterrupto SMB para Windows e Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Introdução ao Armazenamento de Arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md)

### <a name="tooling-support-for-file-storage"></a>Suporte de ferramentas para o armazenamento de arquivos
* [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)
* [Criar e gerenciar compartilhamentos de arquivos](storage-azure-cli.md#create-and-manage-file-shares) usando a CLI do Azure

### <a name="reference"></a>Referência
* [Referência à API REST do serviço de arquivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Artigo de Solução de problemas de arquivos do Azure](storage-troubleshoot-file-connection-problems.md)

### <a name="blog-posts"></a>Postagens no blog
* [O Armazenamento de arquivos do Azure agora está disponível ao público geral](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Por dentro do Armazenamento de arquivos do Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Apresentando o serviço de arquivo do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persistindo conexões para arquivos do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

