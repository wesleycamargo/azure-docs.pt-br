---
title: Usar o Arquivos do Azure com o Linux | Microsoft Docs
description: Saiba como montar um compartilhamento de arquivos do Azure com SMB no Linux.
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
ms.date: 12/20/2017
ms.author: renash
ms.openlocfilehash: cca0d315a815faca5db07099b8e8e451ef55fad5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="use-azure-files-with-linux"></a>Usar o Arquivos do Azure com o Linux
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem de fácil acesso da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o [cliente de kernel CIFS](https://wiki.samba.org/index.php/LinuxCIFS). Este artigo mostra duas maneiras de montar um compartilhamento de arquivos do Azure: sob demanda com o comando `mount` e na inicialização criando uma entrada em `/etc/fstab`.

> [!NOTE]  
> Para montar um compartilhamento de arquivos do Azure fora da região do Azure no qual ele está hospedado, como local ou em uma região diferente do Azure, o sistema operacional deve dar suporte à funcionalidade de criptografia do SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Pré-requisitos para montar um compartilhamento de arquivos do Azure com o Linux e o pacote cifs-utils
* **Escolha uma distribuição de Linux que pode ter o pacote de utilitários cifs instalado.**  
    As distribuições de Linux a seguir estão disponíveis para uso na galeria do Azure:

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8+
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**O pacote de utilitários cifs está instalado.**  
    O pacote cifs-utils pode ser instalado usando o gerenciador de pacotes na distribuição do Linux escolhida. 

    Em distribuições **Ubuntu** e **Debian**, use o gerenciador de pacotes do `apt-get`:

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    No **RHEL** e **CentOS**, use o gerenciador de pacotes do `yum`:

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    No **openSUSE**, use o gerenciador de pacotes do `zypper`:

    ```
    sudo zypper install samba*
    ```

    Em outras distribuições, use o gerenciador de pacotes apropriado ou [compile do código-fonte](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* <a id="smb-client-reqs"></a>**Entenda os requisitos do cliente SMB.**  
    O Arquivos do Azure podem ser montados por meio do SMB 2.1 e do SMB 3.0. Para conexões de clientes no local ou em outras regiões do Azure, o Arquivos do Azure rejeitará SMB 2.1 (ou SMB 3.0 sem criptografia). Se *transferência segura é necessária* estiver habilitada para uma conta de armazenamento, o Arquivos do Azure só permitirá conexões usando o SMB 3.0 com criptografia.
    
    Suporte a criptografia SMB 3.0 foi introduzido na versão 4.11 do kernel do Linux e foi backported para versões mais antigas do kernel para distribuições populares do Linux. No momento da publicação deste documento, as seguintes distribuições da galeria do Azure oferecem suporte a esse recurso:

    - Ubuntu Server 16.04+
    - openSUSE 42.3+
    - SUSE Linux Enterprise Server 12 SP3+
    
    Se a distribuição de Linux não estiver listada aqui, você pode verificar a versão do kernel do Linux com o seguinte comando:

    ```
    uname -r
    ```

* **Decida as permissões de diretório/arquivo do compartilhamento montado**: nos exemplos abaixo, é usada a permissão `0777` para conceder as permissões de leitura, gravação e execução para todos os usuários. Você pode substituí-las por outras [permissões chmod](https://en.wikipedia.org/wiki/Chmod) se desejar. 

* **Nome da conta de armazenamento**: para montar um compartilhamento de arquivos do Azure, você precisará do nome da conta de armazenamento.

* **Chave de conta de armazenamento**: para montar um compartilhamento de arquivos do Azure, você precisa da chave de armazenamento primária (ou secundária). Atualmente, as chaves SAS não têm suporte para montagem.

* **Verifique se a porta 445 está aberta**: o SMB se comunica pela porta TCP 445, por isso confira se o firewall não está bloqueando as portas TCP 445 do computador cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montar o compartilhamento de arquivos do Azure sob demanda com `mount`
1. **[Instale o pacote cifs-utils para sua distribuição Linux](#install-cifs-utils)**.

2. **Crie uma pasta para o ponto de montagem**: uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é comum criá-la na pasta `/mnt`. Por exemplo: 

    ```
    mkdir /mnt/MyAzureFileShare
    ```

3. **Use o comando de montagem para montar o compartilhamento de arquivos do Azure**: lembre-se de substituir `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` e `<mount-point>` pelas informações apropriadas para o seu ambiente. Se a sua distribuição de Linux oferece suporte a SMB 3.0 com criptografia (consulte [Entender os requisitos do cliente SMB](#smb-client-reqs) para obter mais informações), use `3.0` para `<smb-version>`. Para distribuições do Linux que não têm suporte a SMB 3.0 com criptografia, use `2.1` para `<smb-version>`. Observe que um compartilhamento de arquivos do Azure só pode ser montado fora de uma região do Azure (incluindo local ou em uma região diferente do Azure) com SMB 3.0. 

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Quando tiver terminado de usar o compartilhamento de arquivos do Azure, você pode usar `sudo umount <mount-point>` para desmontar o compartilhamento.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Criar um ponto de montagem persistente para o compartilhamento de arquivos do Azure com `/etc/fstab`
1. **[Instale o pacote cifs-utils para sua distribuição Linux](#install-cifs-utils)**.

2. **Crie uma pasta para o ponto de montagem**: uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é comum criá-la na pasta `/mnt`. Sempre que você criá-lo, observe o caminho absoluto da pasta. Por exemplo, o comando a seguir cria uma nova pasta em `/mnt` (o caminho é um caminho absoluto).

    ```
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Use este comando para acrescentar a linha a seguir a `/etc/fstab`**: lembre-se de substituir `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` e `<mount-point>` pelas informações apropriadas para seu ambiente. Se a sua distribuição de Linux oferece suporte a SMB 3.0 com criptografia (consulte [Entender os requisitos do cliente SMB](#smb-client-reqs) para obter mais informações), use `3.0` para `<smb-version>`. Para distribuições do Linux que não têm suporte a SMB 3.0 com criptografia, use `2.1` para `<smb-version>`. Observe que um compartilhamento de arquivos do Azure só pode ser montado fora de uma região do Azure (incluindo local ou em uma região diferente do Azure) com SMB 3.0. 

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Você pode usar `sudo mount -a` para montar o compartilhamento de arquivos do Azure após a edição de `/etc/fstab` em vez de reinicializar.

## <a name="feedback"></a>Comentários
Usuários do Linux, queremos ouvir sua opinião!

O grupo do Arquivos do Azure para usuários do Linux oferece um fórum para que você possa compartilhar comentários à medida que você avalia e adota o Armazenamento de Arquivos no Linux. Envie um email para [Usuários do Linux do Arquivos do Azure](mailto:azurefileslinuxusers@microsoft.com) para ingressar no grupo de usuários.

## <a name="next-steps"></a>Próximas etapas
Veja estes links para obter mais informações sobre o Arquivos do Azure.
* [Introdução aos Arquivos do Azure](storage-files-introduction.md)
* [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
* [Perguntas frequentes](../storage-files-faq.md)
* [Solução de problemas](storage-troubleshoot-linux-file-connection-problems.md)