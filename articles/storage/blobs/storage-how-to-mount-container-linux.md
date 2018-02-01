---
title: Como montar Armazenamento de Blobs do Azure como um sistema de arquivos no Linux | Microsoft Docs
description: "Montar um contêiner de Armazenamento de Blobs do Azure com FUSE no Linux"
services: storage
documentationcenter: linux
author: seguler
manager: jahogg
ms.service: storage
ms.devlang: bash
ms.topic: article
ms.date: 01/19/2018
ms.author: seguler
ms.openlocfilehash: 299b96c783fb3606347bb448d00d44f0071da429
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse-preview"></a>Como montar o Armazenamento de Blobs como um sistema de arquivos com blobfuse (versão prévia)

## <a name="overview"></a>Visão geral
[Blobfuse](https://github.com/Azure/azure-storage-fuse) é um driver de sistema de arquivos virtual para o Armazenamento de Blobs do Azure que permite acessar os Dados do Blob de Blocos existentes em sua conta de Armazenamento através do sistema de arquivos Linux. Armazenamento de Blobs do Azure é um serviço de armazenamento de objeto e, portanto, não possui um namespace hierárquico. O Blobfuse fornece esse namespace usando o esquema de diretório virtual com o uso de barra "/" como um delimitador.  

Este guia mostra como usar o blobfuse e montar um contêiner de Armazenamento de Blobs no Linux e acessar dados. Para saber mais sobre blobfuse, leia os detalhes no [repositório do blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> O blobfuse não garante a conformidade 100% POSIX, pois simplesmente move solicitações em [APIs REST de Blob](https://docs.microsoft.com/en-us/rest/api/storageservices/blob-service-rest-api). Por exemplo, as operações de renomeação são atômicas no POSIX, mas não em blobfuse.
> Para obter uma lista completa de diferenças entre um sistema de arquivos nativo e blobfuse, visite o [repositório de código-fonte do blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalar o blobfuse no Linux
Os binários do blobfuse estão disponíveis em [repositórios de software da Microsoft para Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software). Para instalar o blobfuse, configure um desses repositórios.

### <a name="configure-the-microsoft-package-repository"></a>Configurar o repositório de pacotes da Microsoft
Configure o [Repositório de Pacotes do Linux para Produtos da Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Como exemplo, em uma distribuição Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Da mesma forma, altere a URL para `.../rhel/7/...` e apontar uma distribuição Enterprise Linux 7.

Outro exemplo em um Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Da mesma forma, altere a URL para `.../ubuntu/16.04/...` e apontar uma distribuição Ubuntu 16.04.

### <a name="install-blobfuse"></a>Instalar blobfuse

Em uma distribuição Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

Em uma distribuição Enterprise Linux:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Preparar para a montagem
O blobfuse requer um caminho temporário no sistema de arquivos para armazenar em cache e buffer todos os arquivos abertos, o que ajuda a fornecer desempenho nativo. Para esse caminho temporário, escolha o disco de alto desempenho ou use um ramdisk para obter melhor desempenho. 

> [!NOTE]
> O blobfuse armazena todos os conteúdos do arquivo aberto no caminho temporário. Certifique-se de haver espaço suficiente para acomodar todos os arquivos abertos. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Opcional) Use um ramdisk para o caminho temporário
O exemplo a seguir cria um ramdisk de 16 GB, além de criar um diretório para blobfuse. Escolha o tamanho com base nas suas necessidades. Esse ramdisk permite que o blobfuse abra arquivos com até 16 GB de tamanho. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Use um SSD para o caminho temporário
No Azure, é possível usar os discos efêmeros (SSD) disponíveis em suas VMs para fornecer um buffer de baixa latência para blobfuse. Nas distribuições Ubuntu, esse disco efêmero é montado em '/mnt', enquanto que nas distribuições RedHat e CentOS é montado em '/mnt/resource/'.

Certifique-se de que seu usuário tenha acesso ao caminho temporário:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configurar as credenciais da conta de armazenamento
O Blobfuse exige que suas credenciais sejam armazenadas em um arquivo de texto no seguinte formato: 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

Após criar esse arquivo, certifique-se de restringir o acesso para que nenhum outro usuário possa fazer a leitura.
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>Criar um diretório vazio para montagem
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montar

> [!NOTE]
> Para obter uma lista completa de opções de montagem, verifique o [repositório do blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Para montar o blobfuse, execute o seguinte comando com seu usuário. Esse comando monta o contêiner especificado em '/path/to/fuse_connection.cfg'  no local '/mycontainer'.

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Agora você deverá ter acesso aos blobs de blocos através das APIs do sistema de arquivos regulares. Note que o diretório montado somente poderá ser acessado pelo usuário que fez a montagem, que garante o acesso. Caso queira permitir acesso a todos os usuários, é possível montar através da opção ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Próximas etapas

* [Home page do blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Relatório de problemas de blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

