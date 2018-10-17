---
title: Transferir dados com o Azure Data Box Edge | Microsoft Docs
description: Saiba como adicionar e conectar-se a compartilhamentos no dispositivo do Data Box Edge.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: b5c63a255547bae03acbec771593eac97d474003
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832446"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge-preview"></a>Tutorial: transferir dados com o Azure Data Box Edge (versão prévia)

Este tutorial descreve como adicionar e conectar-se a compartilhamentos no Azure Data Box Edge. Depois que os compartilhamentos são adicionados, o dispositivo do Data Box Edge pode transferir dados para o Azure.

Esse procedimento leva cerca de 10 minutos para ser concluído. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Conectar-se para compartilhar

> [!IMPORTANT]
> O Data Box Edge está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução. 
 
## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar compartilhamentos ao Data Box Edge, certifique-se de que:

* Você instalou o dispositivo físico conforme detalhado em [Install a Data Box Edge](data-box-edge-deploy-install.md) (Instalar um Data Box Edge). 

    O dispositivo físico está ativado, conforme descrito em [Connect and activate your Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md) (Conectar e ativar seu Azure Data Box Edge). O dispositivo está pronto para você criar compartilhamentos e transferir dados.


## <a name="add-a-share"></a>Adicionar um compartilhamento

Execute as etapas a seguir no [portal do Azure](https://portal.azure.com/) para criar um compartilhamento.

1. Volte para o portal do Azure. Vá até **Todos os recursos** e pesquise pelo recurso Data Box Edge.
    
2. Na lista filtrada de recursos, selecione o recurso Data Box Edge e, em seguida, vá até **Visão geral**. Clique em **+ Adicionar compartilhamento** na barra de comandos do dispositivo.
   
   ![Adicionar um compartilhamento](./media/data-box-edge-deploy-add-shares/click-add-share.png)

3. Em **Adicionar compartilhamento**, especifique as configurações de compartilhamento. Forneça um nome exclusivo para seu compartilhamento. 

   Nomes de compartilhamentos podem conter apenas números, letras minúsculas e hifens. O nome do compartilhamento deve ter entre 3 e 63 caracteres e começar com uma letra ou um número. Cada hífen deve ser precedido e seguido por um caractere que não seja um hífen.
    
    1. Escolha um **Tipo** para o compartilhamento. O tipo pode ser SMB ou NFS, em que SMB é o padrão. SMB é o padrão para clientes do Windows e NFS é usado para clientes Linux. 

    2. Dependendo da escolha entre compartilhamentos SMB ou NFS, as opções apresentadas serão ligeiramente diferentes. 

    3. Você deve fornecer uma conta de armazenamento onde o compartilhamento residirá. Um contêiner será criado na conta de armazenamento com o nome do compartilhamento se o contêiner ainda não existir. Se o contêiner já existir, ele será usado. 
    
    4. Escolha o **Serviço de armazenamento** entre blob de blocos, blobs de página ou arquivos. O tipo do serviço escolhido depende do formato escolhido para os dados que residirão no Azure. Por exemplo, neste caso, queremos que os dados residam como blocos de blob no Azure e, portanto, vamos escolher o Blob de Blocos. Se escolher o Blob de Páginas, você deverá garantir que seus dados sejam alinhados com 512 bytes. Por exemplo, um VHDX sempre é alinhado com 512 bytes.
   
    5. Esta etapa depende do tipo de compartilhamento criado, SMB ou NFS. 
     
        - **Se a criação for de um compartilhamento SMB**: no campo Usuário local com todos os privilégios, escolha **Criar novo** ou **Usar existente**. Se a criação for de um novo usuário local, forneça o **nome de usuário**, a **senha**e **confirme a senha**. Isso atribui as permissões ao usuário local. Depois de atribuir as permissões aqui, você pode usar o Gerenciador de Arquivos para modificar essas permissões.

            Se você marcar **Permitir somente operações de leitura** para esses dados de compartilhamento, terá a opção de especificar usuários somente leitura.

            ![Adicionar compartilhamento SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
        - **Se a criação for de um compartilhamento NFS**: você precisa fornecer os endereços IP dos clientes permitidos que podem acessar o compartilhamento.

            ![Adicionar compartilhamento NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Clique em **Criar** para criar o compartilhamento. 
    
    Você será notificado de que a criação do compartilhamento está em andamento. Depois que o compartilhamento for criado com as configurações especificadas, a folha **Compartilhamentos** será atualizada para refletir o novo compartilhamento. 
    
    ![Lista atualizada de compartilhamentos](./media/data-box-edge-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Conectar-se ao compartilhamento

Agora, você pode se conectar a um ou mais compartilhamentos que criou na etapa anterior. As etapas podem variar dependendo de seu compartilhamento ser SMB ou NFS. 

### <a name="connect-to-an-smb-share"></a>Conectar-se a um compartilhamento SMB

Execute estas etapas no cliente do Windows Server conectado a seu Data Box Edge para se conectar aos compartilhamentos.


1. Abra uma janela de comando. No prompt de comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Quando solicitado, digite a senha do compartilhamento. O exemplo de saída desse comando é apresentado aqui.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60': 
    The command completed successfully. 
    
    C: \Users\DataBoxEdgeUser>
    ```   


2. Pressione Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Clique em **OK**. O File Explorer será aberto. Agora você deverá ser capaz de ver os compartilhamentos que criou como pastas. Selecione e clique duas vezes em um compartilhamento (pasta) para exibir o conteúdo.
 
    ![Conectar-se ao compartilhamento SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Os dados são gravados nesses compartilhamentos da forma em que são gerados e o dispositivo envia os dados para a nuvem.

### <a name="connect-to-an-nfs-share"></a>Conectar-se a um compartilhamento NFS

Execute estas etapas em seu cliente Linux conectado ao Azure Data Box Edge.

1. Verifique se o cliente tem o cliente NFSv4 instalado. Para instalar o cliente NFS, use o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, vá para [Instalar cliente NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Após o cliente NFS ser instalado, use o seguinte comando para montar o compartilhamento NFS criado em seu dispositivo do Data Box Edge:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Antes de montar os compartilhamentos, verifique se os diretórios que atuarão como pontos de montagem no computador local já foram criados. Esses diretórios não devem conter arquivos nem subpastas.

    O exemplo a seguir mostra como se conectar via NFS a um compartilhamento em seu dispositivo do Data Box Edge. O IP do dispositivo é `10.10.10.60`. O compartilhamento `mylinuxshare2` é montado no ubuntuVM. O ponto de montagem do compartilhamento é `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> As seguintes condições são aplicáveis à versão prévia:
> - Depois que um arquivo é criado nos compartilhamentos, não há suporte para a renomeação do arquivo. 
> - A exclusão de um arquivo de um compartilhamento não exclui a entrada na conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Data Box Edge, como:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Conectar-se para compartilhar


Prossiga para o próximo tutorial para aprender a transformar seus dados com o Data Box Edge.

> [!div class="nextstepaction"]
> [Transformar dados com o Data Box Edge](./data-box-edge-deploy-configure-compute.md)


